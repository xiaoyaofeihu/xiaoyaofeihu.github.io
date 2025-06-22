---
title: ConcurrentHashMap 能保证复合操作的原子性吗
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# ConcurrentHashMap 能保证复合操作的原子性吗?

# 题目详细答案
不能，ConcurrentHashMap 是 Java 中用于处理并发访问的线程安全集合类。它通过分段锁机制来提高并发性能。然而，尽管 ConcurrentHashMap 能够保证单个操作的线程安全性，但它不能保证复合操作的原子性。

## 单个操作的线程安全性
ConcurrentHashMap中的单个操作，如put(),get(),remove()，是线程安全的。这意味着多个线程可以同时执行这些操作而不会导致数据不一致或抛出异常。

## 复合操作的原子性
复合操作是指多个基本操作的组合，例如“检查-然后-执行”模式（check-then-act），如：

如果键不存在，则添加一个新的键值对。如果键存在，则更新其值。

这些操作在ConcurrentHashMap中不是原子性的，因为在执行复合操作的过程中，可能会有其他线程对ConcurrentHashMap进行修改，导致数据不一致。例如：

```plain
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// 检查是否包含键，然后添加键值对
if (!map.containsKey("key")) {
    map.put("key", 1);
}
```

上面的代码片段不是线程安全的，因为在containsKey()和put()之间，另一个线程可能已经插入了相同的键。

## 解决方案
为了保证复合操作的原子性，可以使用ConcurrentHashMap提供的原子方法，如computeIfAbsent(),compute(),merge()等。这些方法允许在单个操作中执行复杂的计算，从而保证操作的原子性。

### 使用`computeIfAbsent()`
```plain
map.computeIfAbsent("key", k -> 1);
```

如果键`"key"`不存在，则将其值设置为`1`。此操作是原子性的。

### 使用`compute()`
```plain
map.compute("key", (k, v) -> (v == null) ? 1 : v + 1);
```

此方法允许对键进行计算，如果键不存在，则`v`为`null`，否则可以对其值进行更新。

### 使用`merge()`
```plain
map.merge("key", 1, Integer::sum);
```

如果键`"key"`存在，则将其值与`1`相加，否则将其值设置为`1`。此操作也是原子性的。



> /bg29eue26kgthvqq>