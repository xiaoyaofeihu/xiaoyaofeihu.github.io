---
title: Autowired和@Resource的区别？
date: 2024-09-16 13:33:41
tags:
	- Spring
categories: 面试
--- 

# @Autowired和@Resource的区别

# 口语化答案
好的，面试官，Autowired 和 Resource 都是依赖注入注解。一个是 spring 框架带的，一个是 javaee 框架带的。Autowired 主要是类型注入，Resource 是按照名称注入，名称找不到的话，会按照类型进行注入。Autowired 当存在多个的时候，可以配合Qualifier 来进行使用。一般在实际工作中比较常用 Resource。以上。

# 题目解析
常考题，面试官喜欢问，其实两者比较好区分，记住一个是类型，一个是名称即可。然后就是提供方的不同。

# 面试得分点
类型注入，名称注入，指定名称。

# 题目详细答案
@Autowired和@Resource都是用于依赖注入的注解。

### @Autowired
**来源**：Spring 框架。

**注入方式**：默认按类型注入。

**用法**：可以用于字段、构造器、Setter 方法或其他任意方法。

**可选性**：可以与@Qualifier一起使用，以指定具体的 Bean。

**处理机制**：Spring 的AutowiredAnnotationBeanPostProcessor处理@Autowired注解。

### @Resource
**来源**：由 Java EE 提供。

**注入方式**：默认按名称注入，如果按名称找不到，则按类型注入。

**用法**：可以用于字段或 Setter 方法。

**属性**：可以指定name和type属性。

**处理机制**：Spring 的CommonAnnotationBeanPostProcessor处理@Resource注解。

### 详细比较
1. **注入方式**：

@Autowired：默认按类型注入。如果需要按名称注入，可以结合@Qualifier注解使用。

@Resource：默认按名称注入。如果名称匹配失败，则按类型注入。

2. **属性**：

@Autowired：没有name和type属性，但可以使用@Qualifier指定名称。

@Resource：有name和type属性，可以明确指定要注入的 Bean 名称或类型。

3. **兼容性**：

@Autowired：是 Spring 框架特有的注解。

@Resource：是 Java 标准注解，兼容性更广，适用于任何支持 JSR-250 的容器。

4. **使用场景**：

@Autowired：在 Spring 应用中更为常见，尤其是在需要按类型注入的场景中。

@Resource：在需要兼容标准 Java EE 规范的应用中更为常见，或者在需要明确指定 Bean 名称时使用。
