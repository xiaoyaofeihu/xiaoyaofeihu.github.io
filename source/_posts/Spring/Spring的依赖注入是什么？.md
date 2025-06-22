---
title: 👌Spring 的依赖注入是什么？
date: 2025-03-02 13:33:41
categories: Spring
---
# 👌Spring 的依赖注入是什么？

# 口语化答案
好的，面试官，依赖注入（Dependency Injection，简称DI）是Spring框架实现控制反转（IoC）的主要手段。DI的核心思想是将对象的依赖关系从对象内部抽离出来，通过外部注入的方式提供给对象。这样，依赖对象的创建和管理由Spring容器负责，而不是由对象自身负责，使得代码更加模块化、松耦合和易于测试。以上。

# 题目解析
重点题，三大概念之一。di，ioc，aop 之一，大家一定要整明白。

# 面试得分点
构造函数，setter，注解

# 详细答案
在传统编程中，一个对象通常会自己创建它所依赖的其他对象。这种方式使得代码紧密耦合，不利于维护和测试。依赖注入通过将依赖关系从代码中移除，转而由外部容器（如Spring容器）来注入，从而实现了对象之间的松耦合。

## 依赖注入的类型
Spring框架主要提供了三种依赖注入的方式：

**构造函数注入**：通过构造函数将依赖对象传递给被依赖对象。

```plain
public class Service {
    private final Repository repository;

    public Service(Repository repository) {
        this.repository = repository;
    }
}
```

**Setter方法注入**：通过Setter方法将依赖对象注入到被依赖对象中。

```plain
public class Service {
    private Repository repository;

    public void setRepository(Repository repository) {
        this.repository = repository;
    }
}
```

**字段注入**：直接在字段上使用注解进行注入。

```plain
public class Service {
    @Autowired
    private Repository repository;
}
```

## 依赖注入的配置方式
**XML配置**：通过XML文件定义Bean及其依赖关系。

```plain
<beans>
    <bean id="repository" class="com.example.Repository"/>
    <bean id="service" class="com.example.Service">
        <constructor-arg ref="repository"/>
    </bean>
</beans>
```

**Java配置**：通过Java类和注解定义Bean及其依赖关系。

```plain
@Configuration
public class AppConfig {
    @Bean
    public Repository repository() {
        return new Repository();
    }

    @Bean
    public Service service() {
        return new Service(repository());
    }
}
```

**注解配置**：通过注解（如@Component,@Autowired）自动扫描和注入Bean。

```plain
@Component
public class Repository {
}

@Component
public class Service {
    @Autowired
    private Repository repository;
}
```