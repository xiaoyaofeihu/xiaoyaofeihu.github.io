---
title: 解决hash碰撞的方法
date: 2024-11-16 13:33:41
tags:
	- 集合
categories: 面试
---

# 解决hash碰撞的方法？

# 题目详细答案
## 链地址法（Chaining）
链地址法是最常见的解决哈希碰撞的方法之一。在这种方法中，每个桶（bucket）包含一个链表（或树结构，Java 8 及以上版本）。当发生哈希碰撞时，新的键值对被添加到相应桶的链表中。

**优点：**

简单易实现。

动态调整链表长度，不需要提前知道元素数量。

**缺点：**

当链表长度增加时，查找效率下降。

需要额外的存储空间来存储指针。

```plain
class HashMapNode<K, V> {
    K key;
    V value;
    HashMapNode<K, V> next;

    HashMapNode(K key, V value) {
        this.key = key;
        this.value = value;
    }
}
```

## 开放地址法（Open Addressing）
开放地址法不使用链表，而是在哈希表本身寻找空闲位置来存储碰撞的元素。常见的开放地址法有以下几种：

### 线性探测（Linear Probing）
当发生哈希碰撞时，线性探测法在哈希表中向后依次查找下一个空闲位置。

优点：实现简单，不需要额外的存储空间。

缺点：当哈希表接近满时，查找效率急剧下降（称为“主群集”问题）。

```plain
int hash = key.hashCode() % table.length;
while (table[hash] != null) {
    hash = (hash + 1) % table.length;
}
table[hash] = new Entry(key, value);
```

### 二次探测（Quadratic Probing）
二次探测法在发生哈希碰撞时，按照平方序列查找空闲位置（如 1, 4, 9, 16, ...）。

优点：减少主群集问题。

缺点：实现较复杂，可能会导致二次群集问题。

```plain
int hash = key.hashCode() % table.length;
int i = 1;
while (table[hash] != null) {
    hash = (hash + i * i) % table.length;
    i++;
}
table[hash] = new Entry(key, value);
```

### 双重散列（Double Hashing）
双重散列法使用两个不同的哈希函数。当第一个哈希函数发生碰撞时，使用第二个哈希函数计算新的索引。

优点：减少群集问题。较好的查找性能。

缺点：实现复杂。需要设计两个有效的哈希函数。

```plain
int hash1 = key.hashCode() % table.length;
int hash2 = 1 + (key.hashCode() % (table.length - 1));
while (table[hash1] != null) {
    hash1 = (hash1 + hash2) % table.length;
}
table[hash1] = new Entry(key, value);
```

### 再哈希法（Rehashing）
再哈希法在发生碰撞时，使用不同的哈希函数重新计算哈希值，直到找到空闲位置。

优点：减少群集问题。

缺点：实现复杂。需要设计多个有效的哈希函数。

### 分离链接法
在 Java 8 及以上版本中，当链表长度超过一定阈值（默认是 8）时，链表会转换为红黑树，以提高查找效率。

优点：在高冲突情况下性能较好，动态调整链表和树的长度。

缺点：实现复杂，需要额外的存储空间。

## 其他方法
**Cuckoo Hashing**：使用两个哈希表和两个哈希函数，如果插入时发生冲突，将原来的元素“踢出”并重新插入到另一个哈希表中。

**Hopscotch Hashing**：类似于线性探测，但在插入时会调整元素的位置，使得查找路径更短。

链地址法是最常见的解决哈希碰撞的方法，适用于大多数情况。开放地址法在空间利用率上有优势，但在高负载情况下性能可能下降。再哈希法和其他高级方法适用于特定的高性能需求场景。