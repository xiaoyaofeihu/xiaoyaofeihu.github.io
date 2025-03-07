---
title: Spring 的属性注入方式有哪几种
date: 2025-03-02 13:33:41
categories: Spring
---
# 👌Spring 的属性注入方式有哪几种？

# 口语化答案
好的，面试官，属性注入的方式主要有几种，比如构造器注入，setter 注入，字段注入，value 注入等等。最常见的我们平时用的最多的就是，就是@Resource ，@Autowired 这种，像构造器和 setter 这种，经常在第三方依赖包的一些情况下，会进行使用，比如对接一些阿里云 oss，就体现的很明显。像@Value. 这种就常常用于读取配置文件的属性值，来使用。以上。

# 题目解析
常考题。也是年限低一点的小伙伴会问到，考察一下你对不同注入的情况，有没有一定的选择，以及了解当想注入一个属性的时候，都可以有什么方式的思路。

# 面试得分点
构造器，setter，@value。@resource @autowired。  

# 题目详细答案
属性注入（Dependency Injection, DI）是将依赖对象注入到目标对象中的过程。Spring 支持多种属性注入方式，主要包括构造器注入、Setter 方法注入和字段注入。

## 构造器注入
构造器注入通过类的构造器来注入依赖对象。这种方式在对象创建时就完成了依赖注入，确保了依赖对象的不可变性。

```plain
public class MyBean {
    private final Dependency dependency;

    public MyBean(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

#### XML 配置
```plain
<bean id="myBean" class="com.example.MyBean">
    <constructor-arg ref="dependency"/>
</bean>
<bean id="dependency" class="com.example.Dependency"/>
```

#### 注解配置
```plain
@Component
public class MyBean {
    private final Dependency dependency;

    @Autowired
    public MyBean(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

## Setter 方法注入
Setter 方法注入通过类的 Setter 方法来注入依赖对象。这种方式允许在对象创建后进行依赖注入，并且可以在运行时更改依赖对象。

```plain
public class MyBean {
    private Dependency dependency;

    public void setDependency(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

#### XML 配置
```plain
<bean id="myBean" class="com.example.MyBean">
    <property name="dependency" ref="dependency"/>
</bean>
<bean id="dependency" class="com.example.Dependency"/>
```

#### 注解配置
```plain
@Component
public class MyBean {
    private Dependency dependency;

    @Autowired
    public void setDependency(Dependency dependency) {
        this.dependency = dependency;
    }
}
```

## 字段注入
字段注入（也称为属性注入）直接通过在类的字段上使用注解来注入依赖对象。这种方式不需要 Setter 方法，但不推荐使用，因为它违反了面向对象设计原则，使得依赖关系不够明确。

```plain
@Component
public class MyBean {
    @Autowired
    private Dependency dependency;
}
```

## 通过@Value注解注入基本类型和字符串
除了注入 Bean 对象，Spring 还支持通过@Value注解注入基本类型、字符串和其他值，如从配置文件中读取的值。

```plain
@Component
public class MyBean {
    @Value("${my.property}")
    private String myProperty;
}
```

## 通过环境变量或系统属性注入
```plain
@Component
public class MyBean {
    @Value("#{systemProperties['user.name']}")
    private String userName;
}
```

## 通过@Resource注解注入
```plain
@Component
public class MyBean {
    @Resource(name = "dependency")
    private Dependency dependency;
}
```

## 通过@Inject注解注入
```plain
@Component
public class MyBean {
    @Inject
    private Dependency dependency;
}
```