---
title: fail-safe机制
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌什么是fail-safe机制？

# 题目详细答案
fail-safe机制是与fail-fast机制相对的一种并发处理机制。在 Java 集合框架中，fail-safe迭代器在检测到集合在遍历过程中被修改时，不会抛出异常，而是允许这种修改继续进行。fail-safe迭代器通常是通过在遍历时使用集合的副本来实现的，这样即使原集合被修改，迭代器也不会受到影响。

## 工作原理
fail-safe迭代器在遍历集合时，实际上是遍历集合的一个副本。因此，任何对原集合的修改都不会影响到迭代器正在遍历的副本。这种机制保证了遍历操作的安全性，但也意味着迭代器不能反映集合的实时变化。

## 代码 Demo
```plain
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.Iterator;
public class FailSafeExample {
    publicstaticvoidmain(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            Stringelement= iterator.next();
            System.out.println(element);
            // 在迭代过程中修改集合
            if (element.equals("B")) {
                list.add("D"); // 不会引发 ConcurrentModificationException
            }
        }

        System.out.println("After modification: " + list);
    }
}
```

在上面的代码中，使用CopyOnWriteArrayList作为集合。CopyOnWriteArrayList是一个典型的fail-safe集合类，它在每次修改时都会创建集合的一个副本，因此迭代器不会检测到并发修改，不会抛出ConcurrentModificationException。

## 主要特点
1. **不抛异常**：fail-safe迭代器在检测到集合被修改时，不会抛出ConcurrentModificationException异常。
2. **副本遍历**：fail-safe迭代器遍历的是集合的一个副本，而不是原集合。这意味着对原集合的修改不会影响迭代器的遍历。
3. **线程安全**：fail-safe集合类（如CopyOnWriteArrayList、ConcurrentHashMap等）通常是线程安全的，适用于并发环境。

## 常见的fail-safe集合类
CopyOnWriteArrayList，ConcurrentHashMap，ConcurrentLinkedQueue，ConcurrentSkipListMap，ConcurrentSkipListSet

## 注意事项
1. **性能开销**：由于fail-safe机制通常需要创建集合的副本，因此在修改频繁的场景下，性能开销较大。适用于读多写少的场景。
2. **一致性问题**：由于迭代器遍历的是集合的副本，因此它不能反映集合的实时变化。如果需要实时一致性，fail-safe机制可能不适用。

