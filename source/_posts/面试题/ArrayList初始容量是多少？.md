---
title: ArrayList初始容量是多少
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌ArrayList初始容量是多少？

# 题目详细答案
ArrayList 是 Java 中用于动态数组的一个类。它可以在添加或删除元素时自动调整其大小。然而，ArrayList 有一个默认的初始容量，这个容量是在你创建 ArrayList 实例时如果没有明确指定容量参数时所使用的。

在 Java 的 ArrayList 实现中，默认的初始容量是 10。这意味着当你创建一个新的 ArrayList 而不指定其容量时，它会以一个内部数组长度为 10 的数组来开始。当添加的元素数量超过这个初始容量时，ArrayList 的内部数组会进行扩容，通常是增长为原来的 1.5 倍。

```plain
ArrayList<String> list = new ArrayList<>(); // 默认的初始容量是 10
```

但是，如果你知道你将要在 ArrayList 中存储多少元素，或者预计会存储多少元素，那么最好在创建时指定一个初始容量，这样可以减少由于扩容而导致的重新分配数组和复制元素的操作，从而提高性能。

```plain
ArrayList<String> list = new ArrayList<>(50); // 初始容量设置为 50
```

<font style="color:rgb(51, 51, 51);">自从1.7之后，arraylist初始化的时候为一个空数组。但是当你去放入第一个元素的时候，会触发他的懒加载机制，使得数量变为10。</font>

```plain
private static int calculateCapacity(Object[] elementData, int minCapacity) {
if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
return Math.max(DEFAULT_CAPACITY, minCapacity);        
}        
return minCapacity;    
}
```

<font style="color:rgb(51, 51, 51);">所以我们的arraylist初始容量的确是10。只不过jdk8变为懒加载来节省内存。进行了一点优化。</font>





> /sp17yx6dqwcrail5>