---
title: 👌Spring事务传播行为
date: 2025-03-05 13:33:41
categories: Spring
---
# 👌Spring事务传播行为

# 口语化回答
好的，面试官，传播行为主要有几种，REQUIRED，如果当前存在事务，则加入，否则创建一个新的。这是默认的事务传播行为。还有就是其他的不常用的事务形式，比如REQUIRES_NEW，总是创建一个新的事务。如果当前存在事务，则挂起当前事务。SUPPORTS总是创建一个新的事务。使用方式就是@Transactional 里面的propagation 属性进行直接指定即可。其他不常见的就不多提了，就是围绕有事务，无事务，嵌套，以及异常来分不同情况选择事务创建，以上。

# 题目解析
问的比较少，大家就记住前两种就 ok 了。如果全部理解的口诀，就是，有用，没有创建，异常建，没则抛异常。

# 面试得分点
required，Transactional

# 题目详细答案
Spring 事务传播行为定义了在事务方法被调用时，事务如何传播。Spring 提供了多种传播行为，允许开发者定义事务的边界和行为。这些传播行为通过@Transactional注解的propagation属性来配置。

## 传播行为类型
**REQUIRED**（默认值）：如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

**REQUIRES_NEW：**总是创建一个新的事务。如果当前存在事务，则挂起当前事务。

**SUPPORTS：**如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务方式继续执行。

**NOT_SUPPORTED：**总是以非事务方式执行，如果当前存在事务，则挂起当前事务。

**MANDATORY：**如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。

**NEVER：**总是以非事务方式执行，如果当前存在事务，则抛出异常。

**NESTED：**如果当前存在事务，则在嵌套事务内执行；如果当前没有事务，则创建一个新的事务。

## 代码 Demo
假设有两个服务类ServiceA和ServiceB，可以通过不同的传播行为来控制事务的传播。

```plain
@Service
public class ServiceA {
    
    @Autowired
    private ServiceB serviceB;

    @Transactional(propagation = Propagation.REQUIRED)
    public void methodA() {
        // Some database operations
        serviceB.methodB();
        // Some more database operations
    }
}

@Service
public class ServiceB {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void methodB() {
        // Some database operations
    }
}
```

ServiceA的methodA方法使用REQUIRED传播行为，这意味着如果methodA被调用时没有事务存在，它将创建一个新的事务。如果methodA被调用时已经有一个事务存在，它将加入该事务。

ServiceB的methodB方法使用REQUIRES_NEW传播行为，这意味着它总是会创建一个新的事务。如果methodB被调用时已经有一个事务存在，该事务将被挂起，直到methodB的事务完成。

## 事务传播行为的选择
**REQUIRED**：大多数情况下使用的默认传播行为，适用于大多数需要事务管理的方法。

**REQUIRES_NEW**：适用于需要独立事务的情况，例如记录日志、审计等操作，即使外层事务回滚，这些操作也应该提交。

**SUPPORTS**：适用于可选事务的情况，例如读取操作，可以在事务内或事务外执行。

**NOT_SUPPORTED**：适用于不需要事务的情况，例如调用外部服务。

**MANDATORY**：适用于必须在事务内执行的方法，例如严格依赖事务上下文的操作。

**NEVER**：适用于必须在非事务上下文中执行的方法。

**NESTED**：适用于需要嵌套事务的情况，例如需要在一个事务内执行多个子事务，并且可以单独回滚子事务。	