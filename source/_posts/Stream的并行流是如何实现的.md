---
title: Stream 
date: 2024-09-10 17:33:00
tags:
	- 并发
categories: 笔记
---
# Stream的并行流是如何实现的
### Stream的并行流是如何实现的？

在Java中，Stream API提供了一种高效且声明式的方式来处理数据集合。并行流（parallel stream）是Stream API中的一个重要特性，它允许开发者利用多核处理器的并行处理能力来提高数据处理的效率。

1. **获取并行流**：
   - 使用`Collection`接口的`parallelStream`方法，或者通过`Stream`接口的`parallel`方法将顺序流转换为并行流。

2. **底层实现**：
   - 并行流底层使用了Java 7中引入的Fork/Join框架。
   - Fork/Join框架旨在将一个大任务分割（fork）成多个小任务，这些小任务可以并行执行，然后再将这些小任务的结果合并（join）成最终结果。
   - 这种分治策略非常适合处理可以递归分解的大规模计算任务。

3. **执行方式**：
   - 并行流通过并发运行的方式执行流的迭代及操作，从而充分利用多核处理器的性能。

### ForkJoinPool和ThreadPoolExecutor的区别

ForkJoinPool和ThreadPoolExecutor都是Java中用于管理线程和并行任务的工具，但它们在实现方式和适用场景上有所不同。

1. **实现方式**：
   - **ForkJoinPool**：基于工作窃取（Work-Stealing）算法实现的线程池。它适用于处理可以递归分解的大规模计算任务。每个线程都有自己的双端队列（deque）来存储任务，当某个线程的任务队列为空时，它会从其他线程的队列中窃取任务来执行。
   - **ThreadPoolExecutor**：基于任务队列和线程池的实现。它适用于处理不同类型的任务，包括计算密集型、IO密集型和混合型任务。ThreadPoolExecutor允许更灵活的任务调度和线程管理。

2. **适用场景**：
   - **ForkJoinPool**：更适合处理可以递归分解的大规模计算任务，如排序、归并、搜索等。
   - **ThreadPoolExecutor**：适用于更广泛的场景，包括异步执行、定时执行和周期性执行等。

3. **性能**：
   - ForkJoinPool通过工作窃取算法来减少线程之间的空闲时间，提高CPU利用率，但在任务划分不均衡时可能导致线程饥饿。
   - ThreadPoolExecutor的性能取决于任务类型、线程数量和任务队列的配置。

### 总结

Stream的并行流是Java中处理大规模数据集合的高效工具，它底层使用了Fork/Join框架来实现并行处理。而ForkJoinPool和ThreadPoolExecutor则是Java中用于管理线程和并行任务的两种不同实现方式，它们在实现方式、适用场景和性能上有所不同。开发者应根据具体的应用场景和需求选择合适的工具来优化程序的性能。