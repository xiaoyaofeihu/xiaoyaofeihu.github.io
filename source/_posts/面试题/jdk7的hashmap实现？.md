---
title: jdk7的hashmap实现？
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌jdk7的hashmap实现？

# 题目详细答案
HashMap在 JDK 7 中的实现其实并不复杂，它主要依靠两个数据结构：数组和链表。

首先，HashMap内部有一个数组，这个数组用来存储所有的键值对。每个数组的元素其实是一个链表的头节点。也就是说，如果两个或多个键计算出来的哈希值相同，它们会被存储在同一个数组位置的链表中。

当我们往HashMap里放一个键值对时，HashMap会先根据键的hashCode计算出一个哈希值，然后用这个哈希值决定键值对应该放在数组的哪个位置。如果那个位置是空的，键值对就直接放进去；如果那个位置已经有其他键值对了（也就是发生了哈希冲突），HashMap会把新的键值对放到那个位置的链表上。



举个例子吧，假设我们有一个HashMap，我们要往里面放一个键值对("apple", 1)。HashMap会先计算"apple"的哈希值，然后用这个哈希值决定应该把它放到数组的哪个位置。假如计算出来的位置是 5，如果数组的第 5 个位置是空的，它就直接放进去；如果已经有其他键值对了，比如("banana", 2)，它就会把("apple", 1)加到("banana", 2)的链表上。

取值的时候也类似。假设我们要取"apple"对应的值，HashMap会先计算"apple"的哈希值，然后找到数组的对应位置，再沿着链表找到"apple"对应的节点，最后返回它的值。



需要注意的是，HashMap不是线程安全的。如果多个线程同时修改HashMap，可能会导致一些奇怪的问题，比如死循环。所以在多线程环境下，建议使用ConcurrentHashMap。



总结一下，HashMap在 JDK 7 中主要是通过数组和链表来存储数据，使用哈希值来决定存储位置，并通过链表来解决哈希冲突。它的设计让我们在大多数情况下能够快速地存取数据，但在多线程环境下需要小心使用。

JDK 7 中的HashMap底层实现方式主要基于数组和链表。它通过哈希函数将键映射到数组中的索引位置，从而实现快速的查找和存储操作。

## 数据结构
HashMap主要由以下几部分组成：

**数组（table）**：存储HashMap的核心数据结构。每个数组元素是一个链表的头节点。

**链表（Entry）**：处理哈希冲突的结构。当多个键的哈希值映射到同一个数组索引时，这些键值对会被存储在该索引位置的链表中。

## Entry 类
在 JDK 7 中，HashMap使用一个内部类Entry来表示键值对。Entry类的定义如下：

```plain
static class Entry<K, V> implements Map.Entry<K, V> {
    final K key;
    V value;
    Entry<K, V> next;
    final int hash;

    Entry(int h, K k, V v, Entry<K, V> n) {
        value = v;
        next = n;
        key = k;
        hash = h;
    }

    public final K getKey() {
        return key;
    }

    public final V getValue() {
        return value;
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (!(o instanceof Map.Entry))
            return false;
        Map.Entry e = (Map.Entry) o;
        Object k1 = getKey();
        Object k2 = e.getKey();
        if (k1 == k2 || (k1 != null && k1.equals(k2))) {
            Object v1 = getValue();
            Object v2 = e.getValue();
            if (v1 == v2 || (v1 != null && v1.equals(v2)))
                return true;
        }
        return false;
    }

    public final int hashCode() {
        return (key == null ? 0 : key.hashCode()) ^
               (value == null ? 0 : value.hashCode());
    }

    public final String toString() {
        return getKey() + "=" + getValue();
    }
}
```

## 存储过程
当向HashMap中存储一个键值对时，主要步骤如下：

1. **计算哈希值**：通过键的hashCode()方法计算哈希值，并进一步处理以减少冲突。
2. **确定数组索引**：通过哈希值计算数组索引位置。
3. **插入节点**：如果数组索引位置为空，则直接插入。如果不为空，则需要处理哈希冲突。

## 处理哈希冲突
在 JDK 7 中，HashMap通过链表法处理哈希冲突。当多个键的哈希值映射到同一个数组索引时，这些键值对会被存储在该索引位置的链表中。插入时，新节点会被插入到链表的头部。

## 代码示例
以下是put方法的简化版本，展示了HashMap的存储过程：

```plain
public V put(K key, V value) {
    if (key == null)
        return putForNullKey(value);
    int hash = hash(key.hashCode());
    int i = indexFor(hash, table.length);
    for (Entry<K, V> e = table[i]; e != null; e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k))) {
            V oldValue = e.value;
            e.value = value;
            e.recordAccess(this);
            return oldValue;
        }
    }

    modCount++;
    addEntry(hash, key, value, i);
    return null;
}

void addEntry(int hash, K key, V value, int bucketIndex) {
    if ((size >= threshold) && (null != table[bucketIndex])) {
        resize(2 * table.length);
        hash = (null != key) ? hash(key.hashCode()) : 0;
        bucketIndex = indexFor(hash, table.length);
    }

    createEntry(hash, key, value, bucketIndex);
}

void createEntry(int hash, K key, V value, int bucketIndex) {
    Entry<K, V> e = table[bucketIndex];
    table[bucketIndex] = new Entry<>(hash, key, value, e);
    size++;
}
```

## 取值过程
取值时，通过键计算哈希值和数组索引，然后在链表中查找对应的键值对。

```plain
public V get(Object key) {
    if (key == null)
        return getForNullKey();
    int hash = hash(key.hashCode());
    for (Entry<K, V> e = table[indexFor(hash, table.length)];
         e != null;
         e = e.next) {
        Object k;
        if (e.hash == hash && ((k = e.key) == key || key.equals(k)))
            return e.value;
    }
    return null;
}
```





> /yafahumkqtgcnav4>