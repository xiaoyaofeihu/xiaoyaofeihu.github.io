---
title: linkedHashMap为什么能用来做LRUCache
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌linkedHashMap为什么能用来做LRUCache？

# 题目详细答案
LinkedHashMap 能用来做 LRU 缓存的关键原因在于它可以维护访问顺序，并且通过重写removeEldestEntry方法，可以轻松实现缓存的自动清理。

## 关键特性
**访问顺序**：LinkedHashMap提供了一个构造方法，可以指定是否按照访问顺序来维护键值对的顺序。当accessOrder参数设置为true时，LinkedHashMap将根据每次访问（get或put操作）来调整顺序，把最近访问的键值对移到链表的末尾。

**自动清理**：通过重写removeEldestEntry方法，可以在插入新键值对时自动移除最老的键值对（即链表头部的键值对），从而实现缓存的自动清理。

## 实现 LRU 缓存的步骤
1. 创建一个LinkedHashMap实例，并将accessOrder参数设置为true。
2. 重写removeEldestEntry方法，以便在缓存大小超过预定义的最大容量时自动移除最老的键值对。

## 代码 Demo
```plain
import java.util.LinkedHashMap;
import java.util.Map;

public class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int maxCapacity;

    // 构造函数，初始化最大容量和访问顺序
    public LRUCache(int maxCapacity) {
        super(maxCapacity, 0.75f, true);
        this.maxCapacity = maxCapacity;
    }

    // 重写removeEldestEntry方法，当大小超过最大容量时移除最老的键值对
    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        return size() > maxCapacity;
    }

    public static void main(String[] args) {
        // 创建一个容量为3的LRU缓存
        LRUCache<String, Integer> cache = new LRUCache<>(3);

        // 插入键值对
        cache.put("A", 1);
        cache.put("B", 2);
        cache.put("C", 3);

        // 访问键"A"（使其成为最近使用的）
        cache.get("A");

        // 插入新键值对"D"，导致最老的键值对"B"被移除
        cache.put("D", 4);

        // 打印缓存内容
        System.out.println(cache); // 输出: {C=3, A=1, D=4}
    }
}
```

## 解释
**构造方法**：LRUCache构造方法中调用了LinkedHashMap的构造方法，并将accessOrder参数设置为true，以便按照访问顺序维护键值对的顺序。

**removeEldestEntry 方法**：重写了removeEldestEntry方法，当缓存的大小超过maxCapacity时返回true，从而移除最老的键值对。

**使用示例**：在主方法中创建了一个LRUCache实例，插入了几个键值对，并通过访问键 "A" 来改变其顺序。然后插入一个新键值对 "D"，导致最老的键值对 "B" 被移除。



> /rv2p9mfn43gmr2qn>