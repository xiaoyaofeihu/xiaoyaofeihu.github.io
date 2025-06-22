---
title: 报错
date: 2025-05-16 13:33:41
tags:
	- java
categories: error
---

# easyexcel 导入使用shiro报错
## 一、背景描述
SpringBoot项目，使用Shiro进行权限管理。测试过程中发现执行文件导入时最开始一切正常，但是导入几次之后再次执行导入就会报错，此时执行其他功能一切正常

## 二、问题描述
+ 在使用 EasyExcel 进行多线程导入数据时，可能会遇到 org.apache.shiro.session.UnknownSessionException 异常。这个异常通常与 Apache Shiro 的会话管理有关，尤其是在多线程环境下，如果多个线程尝试访问或操作同一个会话，而没有正确处理线程间的同步，就可能抛出此类异常。
## 三、原因分析
1、会话管理不正确：在多线程环境中，如果多个线程尝试访问或修改同一个用户的会话（Session），而没有适当的管理和同步，就可能导致 UnknownSessionException。

2、会话过期或失效：在多线程环境下，如果会话在某个线程中被修改或过期，而其他线程还在尝试访问该会话，也可能会引发此异常。

## 四、问题分析
+ 排查导入代码发现，只有在执行保存语句的时候获取当前用户使用了Shiro相关代码，因此怀疑此处出现问题。
```
this.operator = (String) SecurityUtils.getSubject().getPrincipal();
```
+ 在此处断点发现，手动新增数据和批量导入数据都执行该语句，但两次获取的Subject不一致。因此阅读源码进行排查。
```java
// 从SecurityUtils中获取Subject源码如下
// package: org.apache.shiro.SecurityUtils
public static Subject getSubject() {
    Subject subject = ThreadContext.getSubject(); // ①
    if (subject == null) {
        subject = (new Subject.Builder()).buildSubject();
        ThreadContext.bind(subject);
    }
    return subject;
}

// 继续跟进上面①中的方法
// package: org.apache.shiro.util.ThreadContext
public static Subject getSubject() {
    return (Subject) get(SUBJECT_KEY); // ②
}

// 继续跟进上面②中的方法
// package: org.apache.shiro.util.ThreadContext
public static Object get(Object key) {
    if (log.isTraceEnabled()) {
        String msg = "get() - in thread [" + Thread.currentThread().getName() + "]";
        log.trace(msg);
    }

    Object value = getValue(key); // ③
    if ((value != null) && log.isTraceEnabled()) {
        String msg = "Retrieved value of type [" + value.getClass().getName() + "] for key [" +
                key + "] " + "bound to thread [" + Thread.currentThread().getName() + "]";
        log.trace(msg);
    }
    return value;
}

// 继续跟进上面③中的方法
// package: org.apache.shiro.util.ThreadContext
private static Object getValue(Object key) {
    Map<Object, Object> perThreadResources = resources.get(); // ④
    return perThreadResources != null ? perThreadResources.get(key) : null;
}

// 上面④中的resources在ThreadContext中定义如下
// package: org.apache.shiro.util.ThreadContext
private static final ThreadLocal<Map<Object, Object>> resources = new InheritableThreadLocalMap<Map<Object, Object>>();
```

根据上面对源码的跟踪，发现Subject是与ThreadLocal也就是线程绑定的。获取Subject时先获取当前线程绑定的Subject，若没有则重新创建并绑定到当前线程。而我导入的时候为了提高导入效率，使用了多线程。到此就发现问题的原因了


## 五、问题分析

+ 假设项目中线程池设置核心线程数量为10，而核心线程默认是不会被超时回收的
    - 可通过threadPoolExecutor.allowCoreThreadTimeOut(true);设置核心线程超时回收

1、当用户A登录后，执行导入操作，从线程池中拿出5个线程，此时这5个线程将绑定用户A的Subject
2、当用户A多次执行导入操作后，线程池全部核心线程与用户A的Subject绑定。用户A退出登录后，线程池并不会将核心线程进行销毁。
后续用户B登录，再次执行导入操作，此时线程池分配线程进行操作，但此时所有的线程都已与用户A绑定，因此获取到的Subject都是用户A的Subject，从Subject中获取session时此session已被销毁，因此报错
```java
// 根据sessionId获取session，获取为空则报错
// package: org.apache.shiro.session.mgt.eis.AbstractSessionDAO
public Session readSession(Serializable sessionId) throws UnknownSessionException {
    Session s = doReadSession(sessionId);
    if (s == null) {
        throw new UnknownSessionException("There is no session with id [" + sessionId + "]");
    }
    return s;
}
```

## 六、建议

多线程时不要使用Shiro相关代码。将用户名作为参数传入，不再单独获取。

## 七、参考文档
https://www.cnblogs.com/bbc2005/articles/15340412.html