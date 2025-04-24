---
title: 👌触发FULL GC的几种情况?
date: 2025-04-21 13:33:41
tags:
	- JVM
categories: 笔记
--- 

# 👌触发FULL GC的几种情况?

# 题目详细答案
## 老年代空间不足
当老年代的空间不足以容纳新的对象时，会触发 Full GC。具体情况包括：

**对象晋升**：在 Minor GC 过程中，如果 Survivor 区空间不足，存活对象会被晋升到老年代。如果老年代空间不足以容纳这些晋升的对象，就会触发 Full GC。

**大对象分配**：分配大对象（如大型数组或字符串）时，如果老年代空间不足以分配这些大对象，也会触发 Full GC。

## 永久代或元空间空间不足
在使用旧版 JVM（如 Java 7 及之前）时，永久代（Permanent Generation）用于存储类的元数据。如果永久代空间不足，会触发 Full GC。在 Java 8 及之后，永久代被元空间（Metaspace）取代，用于存储类的元数据。如果元空间不足，也会触发 Full GC。

## 调用System.gc()
调用System.gc()方法会显式请求 JVM 进行 Full GC。尽管 JVM 不保证一定会执行 Full GC，但通常情况下会触发一次 Full GC。

## CMS垃圾收集器的失败
在使用 CMS 垃圾收集器时，如果并发收集过程中老年代空间不足，CMS 会触发一次 Full GC 作为后备措施。这种情况通常被称为 “promotion failure” 或 “concurrent mode failure”。

## JNI 代码导致的内存不足
某些本地代码（JNI）可能会在堆内存中分配大量对象，导致堆内存不足，从而触发 Full GC。

## 内存碎片
如果堆内存中存在大量碎片，导致无法找到足够大的连续空间来分配新对象，也可能触发 Full GC 以尝试压缩内存和清理碎片。
