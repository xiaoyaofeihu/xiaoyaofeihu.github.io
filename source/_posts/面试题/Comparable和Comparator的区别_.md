---
title: Comparable 和 Comparator 的区别
date: 2024-10-16 13:33:41
tags:
	- 集合
categories: 面试
---
# 👌Comparable 和 Comparator 的区别?

# 题目详细答案
Comparable和Comparator是 Java 中用于排序的两个接口，它们有不同的用途和实现方式。

## Comparable接口
Comparable接口用于定义对象的自然排序顺序。实现此接口的类必须覆盖compareTo方法，该方法用于比较当前对象与指定对象的顺序。

类实现Comparable接口，并覆盖compareTo方法。

### 代码 Demo
```plain
import java.util.*;

class Person implements Comparable<Person> {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public int compareTo(Person other) {
        return Integer.compare(this.age, other.age); // 按年龄排序
    }

    @Override
    public String toString() {
        return name + " (" + age + ")";
    }
}

public class ComparableExample {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));

        Collections.sort(people);

        for (Person person : people) {
            System.out.println(person);
        }
    }
}
```

## Comparator接口
Comparator接口用于定义对象的自定义排序顺序。它允许你定义多个排序标准，而不需要修改对象的类。

创建一个或多个实现Comparator接口的类，并覆盖compare方法。

### 代码 Demo
```plain
import java.util.*;

class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return name + " (" + age + ")";
    }
}

class NameComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.getName().compareTo(p2.getName()); // 按名字排序
    }
}

class AgeComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return Integer.compare(p1.getAge(), p2.getAge()); // 按年龄排序
    }
}

public class ComparatorExample {
    public static void main(String[] args) {
        List<Person> people = new ArrayList<>();
        people.add(new Person("Alice", 30));
        people.add(new Person("Bob", 25));
        people.add(new Person("Charlie", 35));

        // 按名字排序
        Collections.sort(people, new NameComparator());
        System.out.println("按名字排序:");
        for (Person person : people) {
            System.out.println(person);
        }

        // 按年龄排序
        Collections.sort(people, new AgeComparator());
        System.out.println("按年龄排序:");
        for (Person person : people) {
            System.out.println(person);
        }
    }
}
```

## 主要区别
**接口实现位置**：

Comparable：对象类自身实现Comparable接口，定义其自然排序顺序。

Comparator：单独的类或匿名类实现Comparator接口，定义自定义排序顺序。

**方法名称**：

Comparable：实现compareTo方法。

Comparator：实现compare方法。

**排序标准**：

Comparable：只能有一个排序标准（自然顺序）。

Comparator：可以有多个排序标准，可以根据需要定义不同的Comparator实现。

**使用场景**：

Comparable：适用于单一的自然排序顺序，例如字典顺序、数字顺序等。

Comparator：适用于需要多个排序标准的场景，例如按名字排序、按年龄排序等。





> /yz4xnv56p75pof6a>