---
title: CompletableFuture 和 Stream 并行流的区别与使用场景
date: 2025-07-06 13:33:41
categories: 多线程
---

# CompletableFuture 和 Stream 并行流的区别与使用场景

在 Java 并发编程中，`CompletableFuture` 和 `Stream` 的并行流都是处理多线程任务的有效工具，但它们在设计目的、使用场景和特性上有所不同。

## 核心区别

1. **底层线程池**：
   - `CompletableFuture`：允许自定义线程池（通过 `Executor`）。
   - `Stream.parallel()` / `parallelStream`：固定使用 `ForkJoinPool.commonPool()`。

2. **任务编排能力**：
   - `CompletableFuture`：支持链式异步任务（如 `thenApply`, `thenCombine`），便于任务编排。
   - `Stream` 并行流：仅支持简单的数据流操作（如 `map`, `filter`），任务编排能力较弱。

3. **异常处理**：
   - `CompletableFuture`：支持回调式异常处理（如 `exceptionally`）。
   - `Stream` 并行流：异常可能导致整个流终止，异常处理较为粗糙。

4. **适用场景**：
   - `CompletableFuture`：适用于 IO 密集型任务、异步任务编排。
   - `Stream` 并行流：适用于 CPU 密集型任务、纯内存计算。

5. **控制粒度**：
   - `CompletableFuture`：细粒度控制，可单独管理每个任务。
   - `Stream` 并行流：粗粒度控制，整个流并行处理。

6. **顺序保证**：
   - `CompletableFuture`：可通过 `thenApplyAsync` 等方法控制任务顺序。
   - `Stream` 并行流：无严格顺序保证，处理顺序可能不确定。

## 使用场景

1. **适合 `CompletableFuture` 的场景**：
   - IO 密集型任务，如 HTTP 请求、数据库查询。
   - 需要复杂的任务编排，如任务 A 完成后触发任务 B。
   - 需要自定义线程池，以避免影响系统全局线程。
   - 需要精细的异常处理。

2. **适合 `Stream` 并行流的场景**：
   - CPU 密集型计算，如大数据集合的 `map`, `filter`。
   - 无外部依赖的纯内存操作。
   - 代码简洁，无需复杂任务编排。

## 如何选择？

- **需要自定义线程池**：选择 `CompletableFuture`。
- **涉及 IO 操作（网络/数据库）**：选择 `CompletableFuture`。
- **需要任务依赖（A→B→C）**：选择 `CompletableFuture`。
- **纯内存计算、无需复杂任务编排**：选择 `Stream` 并行流。

通过理解这些区别和使用场景，开发者可以根据具体需求选择合适的工具来优化并发任务的执行。