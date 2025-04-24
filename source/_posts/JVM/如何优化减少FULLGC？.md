---
title: 👌如何优化减少 FULL GC？
date: 2025-04-25 13:33:41
tags:
	- JVM
categories: 笔记
--- 
# 👌如何优化减少 FULL GC？

# 题目详细答案
## 调整堆内存大小
**增加堆内存大小**：适当增加堆内存大小，可以减少老年代空间不足的情况，从而减少 Full GC 的发生。可以通过-Xmx和-Xms参数调整最大和最小堆内存大小。

**调整新生代大小**：适当增加新生代（Young Generation）的大小，可以减少对象晋升到老年代的频率，从而减少老年代的压力。可以通过-XX:NewSize和-XX:MaxNewSize参数调整新生代大小。

## 调整垃圾收集器参数
根据应用程序的具体需求，调整垃圾收集器的参数，可以优化垃圾收集行为，比如

**G1 GC 参数**：

-XX:MaxGCPauseMillis=<N>：设置目标最大 GC 暂停时间，G1 GC 会尝试在这个目标时间内完成 GC。

-XX:InitiatingHeapOccupancyPercent=<N>：设置触发混合回收的老年代占用比例。

**CMS 参数**：

-XX:CMSInitiatingOccupancyFraction=<N>：设置触发 CMS GC 的老年代占用比例。

-XX:+UseCMSInitiatingOccupancyOnly：仅在老年代占用达到设定比例时触发 CMS GC。

## 优化对象分配和生命周期
减少对象分配和优化对象生命周期，可以减轻垃圾收集器的负担，从而减少 Full GC 的发生：

**减少短生命周期对象**：尽量减少短生命周期对象的创建，或将其分配在栈上而不是堆上。

**缓存和重用对象**：使用对象池（Object Pool）缓存和重用对象，减少对象分配和垃圾回收的频率。

## 避免显式调用System.gc()
显式调用System.gc()会请求 JVM 进行 Full GC，尽量避免在代码中使用System.gc()，除非有充分的理由和必要性。

## 调整元空间（Metaspace）大小
适当增加元空间大小，可以减少因元空间不足而触发的 Full GC

-XX:MetaspaceSize=<size>：设置初始元空间大小。

-XX:MaxMetaspaceSize=<size>：设置最大元空间大小。