---
title: HashMap
date: 2024-11-15 13:33:41
tags:
	- 集合
categories: 面试
---

## 1、HashMap 线程不安全 key和value能存null值
```java

        HashMap<Object, Object> map = new HashMap<>();
        map.put(1,1);
        map.put(1,1);
        map.put(null,2);
        map.put(null,"12");
        System.out.println(map.get(1));
````
### HashMap 能存null值 不会出现空指针的原因是：HashMap 的key如果是null 底层在去hashCode 的时候是默认赋值为0；

```java
    static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

## 2、ConcurrentHashMap 线程安全是因为加了锁 key和value不能存null值
```java
        ConcurrentHashMap<Object, Object> objectObjectConcurrentHashMap = new ConcurrentHashMap<>();
        objectObjectConcurrentHashMap.put(null, null);
```

### key 如果是null，key.hashCode() 会报空指针

+ 部分原码

```java

    final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {}
                }
            }
    }
```
