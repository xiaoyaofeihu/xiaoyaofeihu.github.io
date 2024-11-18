---
title: 常见的list实现类
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---

# 👌介绍一下常见的list实现类？
## 
ArrayList 是最常用的 List 实现类，线程不安全，内部是通过数组实现的，继承了AbstractList，实现了List。它允许对元素进行快速随机访问。数组的缺点是每个元素之间不能有间隔，当数组大小不满足时需要增加存储能力，就要将已经有数组的数据复制到新的存储空间中。当从 ArrayList 的中间位置插入或者删除元素时，需要对数组进行复制、移动、代价比较高。因此，它适合随机查找和遍历，不适合插入和删除。排列有序，可重复，容量不够的时候，新容量的计算公式为：

newCapacity = oldCapacity + (oldCapacity >> 1)，这实际上是将原容量增加50%（即乘以1.5）。

ArrayList实现了RandomAccess接口，即提供了随机访问功能。RandomAccess是java中用来被List实现，为List提供快速访问功能的。在ArrayList中，我们即可以通过元素的序号快速获取元素对象，这就是快速随机访问。

ArrayList实现java.io.Serializable接口，这意味着ArrayList支持序列化，能通过序列化去传输。

## <font style="color:rgb(51, 51, 51);">LinkedList（链表）</font>
<font style="color:rgb(51, 51, 51);">LinkedList 是用链表结构存储数据的，</font><font style="color:rgb(0, 0, 0);">线程不安全。</font><font style="color:rgb(51, 51, 51);">很适合数据的动态插入和删除，随机访问和遍历速度比较慢，</font><font style="color:rgb(0, 0, 0);">增删快</font><font style="color:rgb(51, 51, 51);">。另外，他还提供了 List 接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作堆栈、队列和双向队列使用。底层使用双向链表数据结构。</font>

<font style="color:rgb(0, 0, 0);">LinkedList是一个继承于AbstractSequentialList的双向链表。它也可以被当作堆栈、队列或双端队列进行操作。</font>

## <font style="color:rgb(51, 51, 51);">Vector（数组实现、线程同步）</font>
<font style="color:rgb(51, 51, 51);">Vector 与 ArrayList 一样，也是通过数组实现的，</font><font style="color:rgb(0, 0, 0);">Vector和ArrayList用法上几乎相同，但Vector比较古老，一般不用。Vector是线程同步的，效率低。</font><font style="color:rgb(51, 51, 51);">即某一时刻只有一个线程能够写 Vector，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费，因此，访问它比访问 ArrayList 慢。默认扩展一倍容量。</font>



> /op8ey0ndh6st3bgz>