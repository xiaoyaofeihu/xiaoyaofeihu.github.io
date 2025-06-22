---
title: 👌Cglib的Enhancer实现动态代理？
date: 2025-03-02 13:33:41
categories: Spring
---
# 👌Cglib的Enhancer实现动态代理？

# 口语化回答
好的，面试官，cglib 代理相比 jdk 动态代理不同的就是不需要被代理的类实现接口。假设我们现在有一个MyService，其中有一个方法是performTask，我们只需要定义一个新的类，实现MethodInterceptor 接口，然后再里面的 intercept 方法实现需要增强的方法。最终通过 cglib 的enhancer 类，先设置父类，父类就是我们要增强的类，再设置 callback 也就是我们要增强的功能。最后使用 create 就生成了 cglib 的一个代理类。以上

# 题目解析
和 jdk 的动态代理异曲同工，相比 jdk 反而 cglib 的更常考一点。大家理解一下下面的代码流程就很容易说出其中的过程。

# 面试得分点
目标类，方法拦截器，创建代理对象

# 题目详细答案
CGLIB是一种强大的代码生成库，能够在运行时生成代理类。与 JDK 动态代理不同的是，CGLIB 不需要接口，可以直接代理具体类。CGLIB 通过创建目标类的子类并覆盖其中的方法来实现代理。

## 实现步骤
**1、 引入 CGLIB 库**：确保在项目中添加 CGLIB 依赖。

**2、 创建目标类**：定义需要代理的具体类。

**3、 创建方法拦截器**：实现MethodInterceptor接口，并在intercept方法中定义代理逻辑。

**4、 创建代理对象**：通过Enhancer类创建代理对象。

## 代码 Demo
假设我们有一个简单的服务类MyService，通过 CGLIB 动态代理为MyService创建一个代理对象，并在方法调用前后添加日志。

#### 引入 CGLIB 依赖
```plain
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
```

#### 创建目标类
```plain
public class MyService {
    public void performTask() {
        System.out.println("Performing task");
    }
}
```

#### 创建方法拦截器
```plain
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class LoggingMethodInterceptor implements MethodInterceptor {
    @Override
    public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
        System.out.println("Logging before method execution: " + method.getName());
        Object result = proxy.invokeSuper(obj, args);
        System.out.println("Logging after method execution: " + method.getName());
        return result;
    }
}
```

#### 创建代理对象并使用
```plain
import net.sf.cglib.proxy.Enhancer;

public class MainApp {
    public static void main(String[] args) {
        // 创建 Enhancer 对象
        Enhancer enhancer = new Enhancer();
        
        // 设置目标类为代理类的父类
        enhancer.setSuperclass(MyService.class);
        
        // 设置方法拦截器
        enhancer.setCallback(new LoggingMethodInterceptor());

        // 创建代理对象
        MyService proxyInstance = (MyService) enhancer.create();
        
        // 调用代理对象的方法
        proxyInstance.performTask();
    }
}
```

### 详细解释
**引入 CGLIB 库**：在项目中添加 CGLIB 依赖，以便使用 CGLIB 提供的类和接口。

**目标类**：MyService是一个具体类，定义了一个方法performTask。

**方法拦截器**：LoggingMethodInterceptor实现了MethodInterceptor接口。它的intercept方法在代理对象的方法调用时被调用。

intercept方法接收四个参数：obj：代理对象。method：被调用的方法。args：方法参数。proxy：用于调用父类方法的代理对象。

在intercept方法中，我们在方法调用前后添加了日志打印，并通过proxy.invokeSuper调用父类的方法。

**创建代理对象**：使用Enhancer类创建代理对象。setSuperclass方法设置目标类为代理类的父类。setCallback方法设置方法拦截器。create方法创建代理对象。

**使用代理对象**：

通过代理对象调用方法时，实际调用的是LoggingMethodInterceptor的intercept方法。

在intercept方法中，首先打印日志，然后通过proxy.invokeSuper调用目标对象的方法，最后再打印日志。