---
title: HashMap、Hashtable和ConcurrentHashMap
date: 2024-10-10 17:33:00
tags:
	- 集合
categories: java
---


在Java中，`HashMap`、`Hashtable`和`ConcurrentHashMap`都是用于存储键值对的数据结构，但它们在使用场景和内部实现上有显著的区别。下面是对这些区别的详细解释：

### 线程安全

1. **HashMap**：
   - **非线程安全**：`HashMap`没有考虑线程安全问题，如果在多线程环境下不加同步地使用`HashMap`，可能会导致数据不一致的问题，如死锁、数据丢失或覆盖。

2. **Hashtable**：
   - **线程安全**：`Hashtable`是线程安全的，它的所有方法都是同步的。这意味着在多线程环境中，同时访问和修改`Hashtable`不会导致数据不一致。但是，由于每个方法都使用同步，这可能导致在高并发环境下性能较低。

3. **ConcurrentHashMap**：
   - **线程安全且高效**：`ConcurrentHashMap`是为了解决在高并发环境下`HashMap`的性能问题和`Hashtable`的同步性能瓶颈而设计的。
   - **JDK 1.7及之前**：使用分段锁（Segment Lock）机制，将整个哈希表分为多个段（Segment），每个段本质上是一个小的哈希表，并且有自己的锁。这样，在高并发情况下，只要多个线程访问的是不同的段，就可以并行处理，提高了并发性能。
   - **JDK 1.8及之后**：取消了分段锁，而是采用了CAS（Compare-And-Swap）操作和`synchronized`关键字来实现更细粒度的锁。每个桶（Node）的锁控制更加精确，只有在必要时才锁定相关的桶，进一步提高了并发性能。

### 继承关系

1. **Hashtable**：
   - 继承自`java.util.Dictionary`类，这是一个抽象类，提供了基本的键值对存储功能。`Hashtable`是最早的键值对存储实现之一，因此在Java早期版本中广泛使用。

2. **HashMap**和**ConcurrentHashMap**：
   - 都继承自`java.util.AbstractMap`抽象类，并实现了`java.util.Map`接口。`AbstractMap`提供了Map接口的部分实现，以减少子类实现的工作量。
   - `HashMap`和`ConcurrentHashMap`都提供了Map接口的所有功能，但在实现上有所不同，以适应不同的使用场景。

### 使用场景

- **HashMap**：适用于单线程环境或不需要考虑线程安全的情况，因为它提供了最好的性能。
- **Hashtable**：适用于多线程环境，但需要同步（即线程安全）的键值对存储。然而，由于性能原因，现在通常推荐使用`ConcurrentHashMap`。
- **ConcurrentHashMap**：适用于高并发环境，提供了线程安全的键值对存储，并且性能优于`Hashtable`。

总之，选择哪种Map实现取决于应用程序的具体需求，包括是否需要线程安全以及并发性能的要求。