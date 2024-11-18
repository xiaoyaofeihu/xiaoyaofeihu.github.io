---
title: BlockingQueue是什么
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌BlockingQueue是什么？

# 题目详细答案
BlockingQueue是 Java 中定义在java.util.concurrent包下的一个接口，它扩展了Queue接口，并添加了阻塞操作。BlockingQueue提供了一种线程安全的机制，用于在多线程环境中处理生产者-消费者问题。

## 特点和功能
**阻塞操作**：BlockingQueue提供了阻塞的put和take方法：

put(E e)：如果队列已满，则阻塞直到有空间可插入元素。

take()：如果队列为空，则阻塞直到有元素可取。

**线程安全**：所有方法都使用内部锁或其他同步机制来确保线程安全。

**多种实现**：BlockingQueue有多种实现方式，适用于不同的场景：

ArrayBlockingQueue：基于数组的有界阻塞队列。

LinkedBlockingQueue：基于链表的可选有界阻塞队列。

PriorityBlockingQueue：支持优先级排序的无界阻塞队列。

DelayQueue：支持延迟元素的无界阻塞队列。

SynchronousQueue：不存储元素的阻塞队列，每个插入操作必须等待一个对应的移除操作。

LinkedTransferQueue：基于链表的无界阻塞队列，支持传输操作。

## 代码 Demo
如何使用BlockingQueue实现生产者-消费者模式：

```plain
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public class BlockingQueueExample {
    public static void main(String[] args) {
        BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(5);

        // 生产者线程
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    System.out.println("Producing: " + i);
                    queue.put(i); // 如果队列已满，阻塞
                    Thread.sleep(100); // 模拟生产时间
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        // 消费者线程
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    Integer value = queue.take(); // 如果队列为空，阻塞
                    System.out.println("Consuming: " + value);
                    Thread.sleep(150); // 模拟消费时间
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}
```

LinkedBlockingQueue被用作BlockingQueue的实现。生产者线程不断地向队列中添加元素，而消费者线程不断地从队列中取出元素。如果队列已满，生产者线程会阻塞，直到有空间可插入元素；如果队列为空，消费者线程会阻塞，直到有元素可取。

## 主要方法
BlockingQueue提供了一些常用的方法，这些方法分为四类：

1. **抛出异常**：

add(E e)：如果队列已满，抛出IllegalStateException。

remove()：如果队列为空，抛出NoSuchElementException。

element()：如果队列为空，抛出NoSuchElementException。

2. **返回特殊值**：

offer(E e)：如果队列已满，返回false。

poll()：如果队列为空，返回null。

peek()：如果队列为空，返回null。

3. **阻塞操作**：

put(E e)：如果队列已满，阻塞直到有空间可插入元素。

take()：如果队列为空，阻塞直到有元素可取。

4. **超时操作**：

offer(E e, long timeout, TimeUnit unit)：在指定的时间内插入元素，如果队列已满，等待直到超时或插入成功。

poll(long timeout, TimeUnit unit)：在指定的时间内取出元素，如果队列为空，等待直到超时或取出成功。



> /wuyri8q61obeufmq>