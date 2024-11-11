---
title: volatile笔记 
date: 2024-11-10 17:33:00
tags:
	- 并发
categories: 笔记
---

## volatile能保证原子性吗？
**volatile本身不是锁**
## 一、原子性

原子性是指一个操作或者多个操作要么全部执行，要么全部不执行，中间不会被其他线程的操作打断。在Java中，原子性通常通过同步机制（如`synchronized`）或者原子类（如`AtomicInteger`、`AtomicLong`等）来保证。

### 为什么需要原子性

在并发编程中，多个线程可能会同时访问和修改同一个共享资源。如果没有适当的同步机制，就可能会出现数据不一致、数据丢失等问题。原子性操作可以确保这些共享资源在并发访问时的正确性和一致性。

### 如何实现原子性操作

1. **使用`synchronized`关键字**：
   `synchronized`关键字可以用来修饰方法或代码块，以确保同一时间只有一个线程可以执行被修饰的代码。这是保证原子性的一种常见方式。

2. **使用原子类**：
   Java提供了一些原子类（如`AtomicInteger`、`AtomicLong`、`AtomicReference`等），这些类内部使用了高效的机制来确保操作的原子性。这些类通常用于实现计数器、状态标志等需要原子性操作的场景。

3. **使用锁（Lock）**：
   Java的`java.util.concurrent.locks`包提供了一些高级的锁机制（如`ReentrantLock`、`ReadWriteLock`等），这些锁提供了比`synchronized`更灵活的同步控制。

4. **使用低级别的原子操作**：
   在某些情况下，开发者可能需要直接使用Java的`Unsafe`类或者JNI（Java Native Interface）来调用操作系统的原子操作指令。这种方式通常用于实现高性能的并发数据结构或算法。

### 示例：使用`AtomicInteger`实现原子性自增

```java
import java.util.concurrent.atomic.AtomicInteger;

public class Test {
    private AtomicInteger number = new AtomicInteger(0);

    public void increase() {
        number.incrementAndGet(); // 原子性自增操作
    }

    public static void main(String[] args) {
        Test atomicDemo = new Test();
        for (int j = 0; j < 10; j++) {
            new Thread(() -> {
                for (int i = 0; i < 1000; i++) {
                    atomicDemo.increase();
                }
            }, String.valueOf(j)).start();
        }

        // 等待所有线程执行完毕
        while (Thread.activeCount() > 2) {
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName() + " final number result = " + atomicDemo.number.get());
    }
}
```

在这个示例中，我们使用了`AtomicInteger`的`incrementAndGet`方法来实现原子性自增操作。由于`AtomicInteger`内部使用了高效的原子操作机制，因此可以保证在多线程环境下的正确性。运行这个程序，你会发现每次的结果都是10000，这与使用`volatile`修饰的变量时的结果不同。

## 二、volatile是如何保证可见性和有序性的？ 
### volatile和可见性 
**对于volatile变量，当对volatile变量进行写操作的时候，JVM会向处理器发送一条lock前缀的指令，将这个缓存中的变量回写到系统主存中。 所以，如果一个变量被volatile所修饰的话，在每次数据变化之后，其值都会被强制刷入主存。而其他处理器的缓存由于遵守了缓存一致性协议，也会把这个变量的值从主存加载到自己的缓存中。这就保证了一个volatile在并发编程中，其值在多个缓存中是可见的。**


## 三、`synchronized`和`volatile` 区别

### synchronized的特点和缺点

`synchronized`是一种加锁机制，用于确保在同一时刻只有一个线程可以执行某个方法或代码块。它的主要优点在于能够确保线程安全，防止多个线程同时修改共享资源导致的数据不一致问题。然而，`synchronized`也存在一些缺点：

1. **性能损耗**：虽然JDK 1.6及以后的版本对`synchronized`进行了多种优化（如适应性自旋、锁消除、锁粗化、轻量级锁和偏向锁等），但加锁和解锁的过程仍然会带来一定的性能损耗。

2. **产生阻塞**：`synchronized`实现的锁本质上是一种阻塞锁。当多个线程同时访问同一段同步代码时，只有一个线程能够获取锁并进入临界区，其他线程则需要在Entry Set中等待。这可能导致线程阻塞和上下文切换，从而影响系统的并发性能。

### volatile的特点和优势

`volatile`是Java虚拟机提供的一种轻量级同步机制，它主要用于确保变量的可见性，并禁止指令重排。与`synchronized`相比，`volatile`具有以下特点和优势：

1. **性能更高**：`volatile`变量的读操作与普通变量几乎无差别，写操作虽然由于需要插入内存屏障而稍慢一些，但在大多数场景下，其开销仍然比锁要低。

2. **禁止指令重排**：`volatile`通过内存屏障来确保变量的可见性和有序性。这意味着，当一个线程修改了`volatile`变量的值后，其他线程能够立即看到这个修改。同时，`volatile`还能够防止编译器和处理器对指令进行重排序，从而避免某些潜在的并发问题。

3. **非阻塞**：与`synchronized`不同，`volatile`不会造成线程的阻塞。它只是确保变量的可见性和有序性，而不会限制线程对共享资源的访问。

### 为什么需要volatile

尽管`synchronized`提供了强大的线程同步功能，但在某些场景下，我们仍然需要`volatile`。这主要是因为：

1. **可见性问题**：在某些情况下，我们可能只需要确保变量的可见性，而不需要对共享资源进行加锁。这时，使用`volatile`就足够了。

2. **指令重排问题**：在某些并发场景中，指令重排可能会导致潜在的问题。而`volatile`能够禁止指令重排，从而避免这些问题。

3. **性能考虑**：在某些对性能要求较高的场景中，使用`volatile`可能比使用`synchronized`更加合适。因为`volatile`的开销更低，不会造成线程的阻塞和上下文切换。

综上所述，`synchronized`和`volatile`各有其特点和适用场景。在Java并发编程中，我们需要根据具体的需求和场景来选择合适的同步机制。