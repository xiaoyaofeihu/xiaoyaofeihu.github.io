---
title: BeanFactory 和 ApplicationContext 的区别？
date: 2025-03-02 13:33:41
categories: Spring
---

# 👌BeanFactory 和 ApplicationContext 的区别？

# 口语化答案
好的，面试官。BeanFactory和ApplicationContext都是用于管理Bean的容器接口。BeanFactory功能相对简单。提供了Bean的创建、获取和管理功能。默认采用延迟初始化，只有在第一次访问Bean时才会创建该Bean。因为功能较为基础，BeanFactory通常用于资源受限的环境中，比如移动设备或嵌入式设备。ApplicationContext是BeanFactory的子接口，提供了更丰富的功能和更多的企业级特性。默认会在启动时创建并初始化所有单例Bean，支持自动装配Bean，可以根据配置自动注入依赖对象。有多种实现，如ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、AnnotationConfigApplicationContext等。以上

# 题目解析
经典题，早几年爱考，现在随着时间的发展，问的比较少，不排除古老面试官，应届生可以重点看一下。

# 面试得分点
初始化时机，延迟，企业级应用场景

# 题目详细答案
BeanFactory和ApplicationContext都是用于管理Bean的容器接口，它们的功能和用途有所不同。

## BeanFactory
BeanFactory是Spring框架的核心接口之一，负责管理和配置应用程序中的Bean。它提供了基本的Bean容器功能，但功能相对简单。BeanFactory提供了Bean的创建、获取和管理功能。它是Spring IoC容器的最基本接口。

BeanFactory默认采用延迟初始化（lazy loading），即只有在第一次访问Bean时才会创建该Bean。这有助于提升启动性能。

因为功能较为基础，BeanFactory通常用于资源受限的环境中，比如移动设备或嵌入式设备。

## ApplicationContext
ApplicationContext是BeanFactory的子接口，提供了更丰富的功能和更多的企业级特性。

不仅提供了BeanFactory的所有功能，还提供了更多高级特性，如事件发布、国际化、AOP、自动Bean装配等。

ApplicationContext默认会在启动时创建并初始化所有单例Bean（除非显式配置为延迟初始化）。这有助于在应用启动时尽早发现配置问题。

ApplicationContext支持自动装配Bean，可以根据配置自动注入依赖对象。

ApplicationContext有多种实现，如ClassPathXmlApplicationContext、FileSystemXmlApplicationContext、AnnotationConfigApplicationContext等，适用于不同的配置方式和场景。

## 具体区别总结
| | BeanFactory | ApplicationContext |
| --- | --- | --- |
| **初始化时机** | 延迟初始化，只有在第一次访问Bean时才创建该Bean。 | 立即初始化，在容器启动时就创建并初始化所有单例Bean。 |
| **特性** | 功能较为基础，只提供Bean的创建、获取和管理功能。 | 提供更多企业级特性，如事件发布、国际化、AOP、自动装配等。 |
| **使用场景** | 适用于资源受限的环境，或者需要延迟初始化的场景。 | 适用于大多数企业级应用 |


## 代码 Demo
#### 使用 BeanFactory
```plain
Resource resource = new ClassPathResource("beans.xml");
BeanFactory beanFactory = new XmlBeanFactory(resource);
MyBean myBean = (MyBean) beanFactory.getBean("myBean");
```

#### 使用 ApplicationContext
```plain
ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
MyBean myBean = (MyBean) context.getBean("myBean");
```
