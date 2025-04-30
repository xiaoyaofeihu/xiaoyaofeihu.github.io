---
title: jvm 的四种引用区别？
date: 2025-04-29 13:33:41
tags:
	- JVM
categories: 笔记
--- 
# 👌jvm 的四种引用区别？

# 题目详细答案
在 Java 中，引用类型的不同决定了垃圾收集器如何处理对象的生命周期。Java 提供了四种引用类型：强引用（Strong Reference）、软引用（Soft Reference）、弱引用（Weak Reference）和虚引用（Phantom Reference）。这些引用类型在java.lang.ref包中定义，它们的区别如下：

## 强引用
强引用是 Java 中最常见的引用类型。通过new关键字创建的对象引用就是强引用。只要强引用存在，垃圾收集器永远不会回收被引用的对象。强引用是默认的引用类型，通常使用最广泛。

```plain
String str=new String("Hello, World!");
```

str是一个强引用，只要str不被置为null或超出作用域，对象"Hello, World!"就不会被垃圾收集器回收。

## 软引用
软引用是一种相对较强但仍允许垃圾收集器回收的引用类型，适用于实现内存敏感的缓存。只有在内存不足的情况下，垃圾收集器才会回收被软引用关联的对象。软引用通常用于实现缓存，当内存充足时对象不会被回收，当内存不足时对象会被回收以释放内存。

```plain
import java.lang.ref.SoftReference;

String str = new String("Hello, World!");
SoftReference<String> softRef = new SoftReference<>(str);
str = null; // 允许 str 对象被垃圾收集器回收
```

softRef是一个软引用，当内存不足时，对象"Hello, World!"可能会被回收。

## 弱引用
弱引用是一种比软引用更弱的引用类型，适用于实现非强制性缓存。只要垃圾收集器运行，不管内存是否充足，都会回收被弱引用关联的对象。弱引用通常用于实现规范化映射（canonical mappings），例如WeakHashMap。

```plain
import java.lang.ref.WeakReference;

String str = new String("Hello, World!");
WeakReference<String> weakRef = new WeakReference<>(str);
str = null; // 允许 str 对象被垃圾收集器回收
```

weakRef是一个弱引用，垃圾收集器在下一次运行时可能会回收对象"Hello, World!"。

## 虚引用
虚引用是一种最弱的引用类型，它仅用于跟踪对象被垃圾收集器回收的时间。虚引用本身不会决定对象的生命周期，垃圾收集器回收对象时会将虚引用放入引用队列中。虚引用通常用于实现一些特殊的清理机制，例如管理直接内存（Direct Memory）。

```plain
import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;

String str = new String("Hello, World!");
ReferenceQueue<String> refQueue = new ReferenceQueue<>();
PhantomReference<String> phantomRef = new PhantomReference<>(str, refQueue);
str = null; // 允许 str 对象被垃圾收集器回收

// 当垃圾收集器回收 str 对象时，phantomRef 会被放入 refQueue
```

phantomRef是一个虚引用，当垃圾收集器回收对象"Hello, World!"时，phantomRef会被放入refQueue中。