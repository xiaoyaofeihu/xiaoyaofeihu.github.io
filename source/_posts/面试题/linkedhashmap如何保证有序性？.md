---
title: linkedhashmap如何保证有序性
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌linkedhashmap如何保证有序性？

# 题目详细答案
LinkedHashMap通过维护一个双向链表来保证有序性。这个双向链表记录了所有插入的键值对的顺序。根据构造方法中的参数设置，LinkedHashMap可以按插入顺序或访问顺序来维护这些键值对的顺序。

## 具体实现原理
1. **双向链表**：LinkedHashMap在内部维护了一个双向链表。每个节点对应一个键值对，并且包含指向前一个节点和后一个节点的引用。通过这个链表，LinkedHashMap可以快速地遍历所有键值对，保持其有序性。
2. **插入顺序**：默认情况下，LinkedHashMap按照键值对插入的顺序来维护顺序。每次插入新键值对时，它会将新节点添加到链表的末尾。
3. **访问顺序**：如果在构造方法中将accessOrder参数设置为true，LinkedHashMap将按照访问顺序来维护键值对的顺序。每次访问（get或put操作）一个键值对时，它会将对应的节点移动到链表的末尾。

## 代码 Demo
```plain
import java.util.LinkedHashMap;
import java.util.Map;

public class LinkedHashMapOrderExample {
    public static void main(String[] args) {
        // 插入顺序
        LinkedHashMap<String, Integer> insertionOrderMap = new LinkedHashMap<>();
        insertionOrderMap.put("A", 1);
        insertionOrderMap.put("B", 2);
        insertionOrderMap.put("C", 3);

        System.out.println("插入顺序:");
        for (Map.Entry<String, Integer> entry : insertionOrderMap.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }

        // 访问顺序
        LinkedHashMap<String, Integer> accessOrderMap = new LinkedHashMap<>(16, 0.75f, true);
        accessOrderMap.put("A", 1);
        accessOrderMap.put("B", 2);
        accessOrderMap.put("C", 3);

        // 访问某些元素
        accessOrderMap.get("A");
        accessOrderMap.get("C");

        System.out.println("访问顺序:");
        for (Map.Entry<String, Integer> entry : accessOrderMap.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

### 解释
**插入顺序**：

创建一个LinkedHashMap实例insertionOrderMap。

插入键值对 "A"、"B" 和 "C"。

遍历并打印键值对，顺序与插入顺序一致。

**访问顺序**：

创建一个LinkedHashMap实例accessOrderMap，并将accessOrder参数设置为true。

插入键值对 "A"、"B" 和 "C"。

访问键 "A" 和 "C"（通过get操作）。

遍历并打印键值对，顺序按照最近访问的顺序排列。

## 内部机制
**节点结构**：LinkedHashMap的每个节点不仅包含键和值，还包含指向前一个节点和后一个节点的引用。这使得它可以高效地维护顺序。

**操作调整**：在每次插入或访问键值对时，LinkedHashMap会调整链表中节点的位置，以确保顺序的正确性。例如，在访问顺序模式下，每次访问一个键值对时，它会将对应的节点移动到链表的末尾。

通过这些机制，LinkedHashMap能够高效地维护键值对的有序性，无论是按插入顺序还是访问顺序。



> /sgl9ep03rn5cwda1>