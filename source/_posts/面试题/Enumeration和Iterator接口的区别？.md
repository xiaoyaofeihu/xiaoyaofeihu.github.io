---
title: Enumeration和Iterator接口的区别
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌Enumeration和Iterator接口的区别？

# 题目详细答案
Enumeration和Iterator是 Java 中用于遍历集合的两个接口。虽然它们有相似的功能，但它们有不同的设计和使用方式。

## Enumeration接口
Enumeration是一个较老的接口，存在于 Java 1.0 中。它主要用于遍历旧的集合类，如Vector和Hashtable。

### 常用方法
boolean hasMoreElements(): 如果枚举中仍有更多元素，则返回true。

Object nextElement(): 返回枚举中的下一个元素。

### 代码 Demo
```plain
import java.util.*;

public class EnumerationExample {
    public static void main(String[] args) {
        Vector<String> vector = new Vector<>();
        vector.add("A");
        vector.add("B");
        vector.add("C");

        Enumeration<String> enumeration = vector.elements();
        while (enumeration.hasMoreElements()) {
            String element = enumeration.nextElement();
            System.out.println(element);
        }
    }
}
```

## Iterator接口
Iterator是在 Java 2 (JDK 1.2) 中引入的。它是集合框架的一部分，适用于所有集合类（如ArrayList、HashSet、HashMap等）。Iterator提供了更灵活的遍历方法，并允许在遍历过程中安全地移除元素。

### 方法
boolean hasNext(): 如果迭代器中仍有更多元素，则返回true。

E next(): 返回迭代器中的下一个元素。

void remove(): 从集合中移除迭代器返回的最后一个元素（可选操作）。

### 代码 Demo
```plain
import java.util.*;

public class IteratorExample {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");

        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()) {
            String element = iterator.next();
            System.out.println(element);
            if (element.equals("B")) {
                iterator.remove(); // 安全地移除元素
            }
        }

        System.out.println("After removal: " + list);
    }
}
```

## 主要区别
**接口引入时间**：

Enumeration：引入于 Java 1.0。

Iterator：引入于 Java 2 (JDK 1.2)。

**方法名称和功能**：

Enumeration：使用hasMoreElements()和nextElement()方法。

Iterator：使用hasNext()和next()方法，并增加了remove()方法。

**元素移除**：

Enumeration：不支持在遍历过程中移除元素。

Iterator：支持在遍历过程中安全地移除元素（通过remove()方法）。

**适用范围**：

Enumeration：主要用于旧的集合类，如Vector和Hashtable。

Iterator：适用于所有集合类，是集合框架的一部分。



> /vk496pkev2ub6nh4>