---
title: HashSet如何实现线程安全
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌HashSet如何实现线程安全？

# 题目详细答案
HashSet本身不是线程安全的。如果多个线程在没有外部同步的情况下同时访问一个HashSet，并且至少有一个线程修改了集合，那么它必须保持同步。

## 使用Collections.synchronizedSet
Java 提供了一个简单的方法来创建一个同步的集合，通过Collections.synchronizedSet方法。这个方法返回一个线程安全的集合包装器。

```plain
Set<String> synchronizedSet = Collections.synchronizedSet(newHashSet<>());
```

使用这个方法后，所有对集合的访问都将是同步的。但是，需要注意的是，对于迭代操作，必须手动同步：

```plain
Set<String> synchronizedSet = Collections.synchronizedSet(newHashSet<>());
synchronized (synchronizedSet) {
    Iterator<String> iterator = synchronizedSet.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());
    }
}
```

## 使用ConcurrentHashMap
如果需要更高效的并发访问，可以使用ConcurrentHashMap来实现类似HashSet的功能。ConcurrentHashMap提供了更细粒度的锁机制，在高并发环境下性能更好。

```plain
Set<String> concurrentSet = ConcurrentHashMap.newKeySet();
```

ConcurrentHashMap.newKeySet()返回一个基于ConcurrentHashMap的Set实现，它是线程安全的，并且在高并发环境下性能优越。

## 使用CopyOnWriteArraySet
对于读操作远多于写操作的场景，可以使用CopyOnWriteArraySet。它的实现基于CopyOnWriteArrayList，在每次修改时都会复制整个底层数组，因此在写操作较少时性能较好。

```plain
Set<String> copyOnWriteArraySet = newCopyOnWriteArraySet<>();
```

## 手动同步
如果你不想使用上述任何一种方法，也可以手动同步HashSet的访问。可以使用synchronized关键字来保护对HashSet的访问：

```plain
Set<String> hashSet = newHashSet<>();
synchronized (hashSet) {
    // 对 hashSet 的操作
}
```

## 选择合适的方案
如果你的应用程序是单线程的，或只有少量的线程访问集合，可以使用Collections.synchronizedSet。

如果你的应用程序有大量的并发读写操作，可以使用ConcurrentHashMap.newKeySet。

如果你的应用程序读操作远多于写操作，可以使用CopyOnWriteArraySet。

## 代码 Demo
再给大家弄一个使用ConcurrentHashMap实现线程安全Set的示例代码：

```plain
import java.util.Set;
import java.util.concurrent.ConcurrentHashMap;

public class ConcurrentHashSetExample {
    public static void main(String[] args) {
        Set<String> concurrentSet = ConcurrentHashMap.newKeySet();

        // 多线程环境下的操作示例
        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                concurrentSet.add(Thread.currentThread().getName() + "-" + i);
            }
        };

        Thread thread1 = new Thread(task, "Thread1");
        Thread thread2 = new Thread(task, "Thread2");

        thread1.start();
        thread2.start();

        try {
            thread1.join();
            thread2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Set size: " + concurrentSet.size());
    }
}
```



> /uic9i6eyh8to1udk>