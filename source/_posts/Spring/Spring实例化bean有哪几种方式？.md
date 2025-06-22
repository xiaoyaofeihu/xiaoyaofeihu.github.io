---
title: 👌Spring 实例化 bean 有哪几种方式？
date: 2025-03-04 13:33:41
categories: Spring
---
# 👌Spring 实例化 bean 有哪几种方式？

# 口语化回答
好的，面试官。实例化 bean 的方式，主要有构造器实例化，bean 注解实例化，不常用的用静态工厂和实例工厂实例化。构造器实例化主要是通过调用类的构造器来实例化 Bean。构造器可以是无参构造器，也可以是有参构造器。配合 bean 标签的 xml 配置形式，放入容器中。bean 注解的方式就更加常用，尤其是现在都是 boot 的形式。一个注解就可以放入容器中。以上。

# 题目解析
这道题也很常问，同样的还是考察是否平时在学习中用过。应届生比较常问，工作的小伙伴问的概率不大。

# 面试得分点
构造器实例化，bean 方式，工厂方式

# 题目详细答案
## 通过构造器实例化
这是最常见的方式之一，Spring 可以通过调用类的构造器来实例化 Bean。构造器可以是无参构造器，也可以是有参构造器。

### 无参构造器
```plain
public class MyBean {
    public MyBean() {
        // 无参构造器
    }
}
```

配置方式：

```plain
<bean id="myBean" class="com.example.MyBean"/>
```

### 有参构造器
```plain
public class MyBean {
    private String name;

    public MyBean(String name) {
        this.name = name;
    }
}
```

配置方式：

```plain
<bean id="myBean" class="com.example.MyBean">
    <constructor-arg value="exampleName"/>
</bean>
```

## 通过静态工厂方法实例化
Spring 可以通过调用静态工厂方法来实例化 Bean。这种方式适用于需要复杂初始化逻辑的情况。

```plain
public class MyBeanFactory {
    public static MyBean createInstance(String name) {
        return new MyBean(name);
    }
}
```

配置方式：

```plain
<bean id="myBean" class="com.example.MyBeanFactory" factory-method="createInstance">
    <constructor-arg value="exampleName"/>
</bean>
```

## 通过实例工厂方法实例化
这种方式类似于静态工厂方法，不同之处在于实例工厂方法需要先实例化工厂类，然后调用工厂类的实例方法来创建 Bean。

```plain
public class MyBeanFactory {
    public MyBean createInstance(String name) {
        return new MyBean(name);
    }
}
```

配置方式：

```plain
<bean id="myBeanFactory" class="com.example.MyBeanFactory"/>
<bean id="myBean" factory-bean="myBeanFactory" factory-method="createInstance">
    <constructor-arg value="exampleName"/>
</bean>
```

## 通过 FactoryBean 接口实例化
Spring 提供了一个FactoryBean接口，允许开发者定制 Bean 的创建逻辑。实现FactoryBean接口的类可以被用作工厂来生成其他 Bean。

```plain
public class MyFactoryBean implements FactoryBean<MyBean> {
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public MyBean getObject() throws Exception {
        return new MyBean(name);
    }

    @Override
    public Class<?> getObjectType() {
        return MyBean.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }
}
```

配置方式：

```plain
<bean id="myFactoryBean" class="com.example.MyFactoryBean">
    <property name="name" value="exampleName"/>
</bean>
<bean id="myBean" factory-bean="myFactoryBean" factory-method="getObject"/>
```

## 通过 @Bean 注解实例化
使用@Bean注解的方法可以用来实例化和配置 Bean。这种方式更加直观和灵活，适合基于 Java 配置的 Spring 应用。

```plain
@Configuration
public class AppConfig {
    @Bean
    public MyBean myBean() {
        return new MyBean("exampleName");
    }
}
```

## 通过 @Component 注解实例化
使用@Component注解可以将类标记为 Spring 管理的 Bean。结合@ComponentScan注解，Spring 会自动扫描并实例化这些类。

```plain
@Component
public class MyBean {
    // Bean 定义
}
```

配置方式：

```plain
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
    // 配置类
}
```