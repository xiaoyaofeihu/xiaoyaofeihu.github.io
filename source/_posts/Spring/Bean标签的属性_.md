---
title: Bean 标签的属性？
date: 2025-03-02 13:33:41
categories: Spring
---

# 👌Bean 标签的属性?

# 口语化回答
好的，面试官。bean 是最长使用的标签，如果是使用 xml 形式。最常见的基本属性就是 id，name，class。分别标识唯一的 bean，bean 的别名和实际要注入的类。也可以通过一些属性实现，bean 开始时候的操作，比如init-method，配置的方法，可以在 bean 初始化的时候，进行执行。bean 还有常见的构造函数注入标签，注入 bean 中的属性。以上。

# 题目解析
应届生常问，有工作经验的基本不问，目的其实就是为了，考察一下你到底有没有实际使用过 spring 的 bean 的配置。

# 面试得分点
id，name，class，一些初始化属性

# 题目详细答案
## 常见属性
**id：**Bean的唯一标识符。

```plain
<bean id="myBean" class="com.example.MyBean"/>
```

**name：**Bean的别名，可以为Bean定义一个或多个别名。

```plain
<bean id="myBean" name="alias1,alias2" class="com.example.MyBean"/>
```

**class：**Bean的全限定类名。

```plain
<bean id="myBean" class="com.example.MyBean"/>
```

**scope：**Bean的作用域，常见值包括singleton（默认）、prototype、request、session、globalSession、application。

```plain
<bean id="myBean" class="com.example.MyBean" scope="prototype"/>
```

**init-method：**Bean初始化时调用的方法。

```plain
<bean id="myBean" class="com.example.MyBean" init-method="init"/>
```

**destroy-method：**Bean销毁时调用的方法。

```plain
<bean id="myBean" class="com.example.MyBean" destroy-method="cleanup"/>
```

**factory-method：**用于创建Bean实例的静态工厂方法。

```plain
<bean id="myBean" class="com.example.MyBeanFactory" factory-method="createInstance"/>
```

**factory-bean：**用于创建Bean实例的工厂Bean的名称。

```plain
<bean id="myFactory" class="com.example.MyFactory"/>
<bean id="myBean" factory-bean="myFactory" factory-method="createInstance"/>
```

## 依赖注入相关属性
**constructor-arg：**用于构造函数注入。

```plain
<bean id="myBean" class="com.example.MyBean">
    <constructor-arg value="someValue"/>
    <constructor-arg ref="anotherBean"/>
</bean>
```

**property：**用于Setter方法注入。

```plain
<bean id="myBean" class="com.example.MyBean">
    <property name="propertyName" value="someValue"/>
    <property name="anotherProperty" ref="anotherBean"/>
</bean>
```

## 其他常用属性
**autowire：**自动装配模式，常见值包括no（默认）、byName、byType、constructor、autodetect。

```plain
<bean id="myBean" class="com.example.MyBean" autowire="byName"/>
```

**depends-on：**指定Bean的依赖关系，即在初始化当前Bean之前需要先初始化的Bean。

```plain
<bean id="myBean" class="com.example.MyBean" depends-on="anotherBean"/>
```

**lazy-init：**是否延迟初始化，默认值为false。

```plain
<bean id="myBean" class="com.example.MyBean" lazy-init="true"/>
```

**primary：**当自动装配时，如果有多个候选Bean，可以将某个Bean标记为主要候选者。

```plain
<bean id="myBean" class="com.example.MyBean" primary="true"/>
```

## 配置 demo
```plain
<bean id="myBean" class="com.example.MyBean" scope="singleton" init-method="init" destroy-method="cleanup" lazy-init="true" primary="true" autowire="byType">
    <constructor-arg value="someValue"/>
    <constructor-arg ref="anotherBean"/>
    <property name="propertyName" value="someValue"/>
    <property name="anotherProperty" ref="anotherBean"/>
</bean>
```

# 