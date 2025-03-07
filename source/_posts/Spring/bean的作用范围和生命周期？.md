---
title: 👌bean 的作用范围和生命周期？
date: 2025-03-02 13:33:41
categories: Spring
---
# 👌bean 的作用范围和生命周期？

# 口语化答案
好的，面试官。bean 的作用范围主要用 singleton，prototype，request，session，globalsession，application。常用的就是 singleton，singleton 是单例的，当 bean 是无状态的时候，singleton 是最好的使用方式，如果说 bean 里面涉及共享数据，singleton 就不够安全了，这个时候需要使用 prototype。bean 的生命周期从实例化创建 bean 开始，然后进行属性设置。再之后，调用 bean 的一些初始化方法，如果有则执行，这样处理完之后，bean 就可以被使用了。最终当 bean 要被销毁的时候，就会调用 destroy 方法进行 bean 的后置处理，以上。

# 题目解析
非常常考，一定要熟悉要 singleton 和 prototype 的区别。这个重点要的是状态问题。bean 的生命周期就比较好答，按照顺序来说就可以了。

# 面试得分点
singleton，prototype，无状态，实例化，属性设置，初始方法，销毁

# 题目详细答案
Bean的作用范围（Scope）和生命周期（Lifecycle）决定了Bean的创建、使用和销毁方式。

## Bean的作用范围（Scope）
**singleton：**默认作用范围，整个Spring容器中只有一个实例，所有对该Bean的引用都指向同一个实例。适用于无状态的Bean。

```plain
<bean id="myBean" class="com.example.MyBean" scope="singleton"/>
```

**prototype：**每次请求该Bean时都会创建一个新的实例。适用于有状态的Bean。

```plain
<bean id="myBean" class="com.example.MyBean" scope="prototype"/>
```

**request：**每次HTTP请求都会创建一个新的实例，仅适用于Web应用。

```plain
<bean id="myBean" class="com.example.MyBean" scope="request"/>
```

**session：**每个HTTP会话都会创建一个新的实例，仅适用于Web应用。

```plain
<bean id="myBean" class="com.example.MyBean" scope="session"/>
```

**globalSession：**每个全局HTTP会话都会创建一个新的实例，仅适用于Portlet应用。

```plain
<bean id="myBean" class="com.example.MyBean" scope="globalSession"/>
```

**application：**每个ServletContext会创建一个新的实例，适用于Web应用。

```plain
<bean id="myBean" class="com.example.MyBean" scope="application"/>
```

## Bean的生命周期
![1724754790092-94de9a92-f85f-4b0f-851d-62174f065951.png](./img/DUD24bv6N1y1lj5S/1724754790092-94de9a92-f85f-4b0f-851d-62174f065951-178583.png)

### 实例化（Instantiation）
Spring容器根据配置创建Bean实例。

### 属性设置（Property Population）
Spring容器进行依赖注入，设置Bean的属性。

### 初始化（Initialization）
如果Bean实现了InitializingBean接口，Spring会调用其afterPropertiesSet()方法。

如果在XML配置中指定了init-method属性，Spring会调用指定的初始化方法。

如果Bean使用了@PostConstruct注解，Spring会调用标注的方法。

### 使用（Usage）
Bean处于就绪状态，可以被应用程序使用。

### 销毁（Destruction）
如果Bean实现了DisposableBean接口，Spring会调用其destroy()方法。

如果在XML配置中指定了destroy-method属性，Spring会调用指定的销毁方法。

如果Bean使用了@PreDestroy注解，Spring会调用标注的方法。

## 生命周期回调接口和注解
**InitializingBean接口**

方法：afterPropertiesSet()

```plain
public class MyBean implements InitializingBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化逻辑
    }
}
```

**DisposableBean接口**

方法：destroy()

```plain
public class MyBean implements DisposableBean {
    @Override
    public void destroy() throws Exception {
        // 销毁逻辑
    }
}
```

**@PostConstruct注解**

用于标注初始化方法。

```plain
public class MyBean {
    @PostConstruct
    public void init() {
        // 初始化逻辑
    }
}
```

**@PreDestroy注解**

用于标注销毁方法。

```plain
public class MyBean {
    @PreDestroy
    public void cleanup() {
        // 销毁逻辑
    }
}
```

## 代码 Demo
```plain
<bean id="myBean" class="com.example.MyBean" scope="singleton" init-method="customInit" destroy-method="customDestroy"/>
```

```plain
public class MyBean implements InitializingBean, DisposableBean {
    @Override
    public void afterPropertiesSet() throws Exception {
        // 初始化逻辑
    }

    @Override
    public void destroy() throws Exception {
        // 销毁逻辑
    }

    @PostConstruct
    public void init() {
        // 初始化逻辑
    }

    @PreDestroy
    public void cleanup() {
        // 销毁逻辑
    }

    public void customInit() {
        // 自定义初始化逻辑
    }

    public void customDestroy() {
        // 自定义销毁逻辑
    }
}
```

# 