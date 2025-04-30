---
title: 👌Java类初始化时机
date: 2025-05-03 13:33:41
tags:
	- JVM
categories: 笔记
--- 
# 👌Java类初始化时机?

# 题目详细答案
## 主动引用
### 创建类的实例
当使用new关键字创建类的实例时，类会被初始化。

```plain
MyClass obj = new MyClass();
```

### 访问类的静态变量或静态方法
当访问类的静态变量或调用静态方法时，类会被初始化。

```plain
System.out.println(MyClass.staticVar);
MyClass.staticMethod();
```

### 反射
通过反射 API 对类进行反射调用时，类会被初始化。

```plain
Class.forName("com.example.MyClass");
```

### 初始化子类
当初始化一个类的子类时，父类会被初始化。

```plain
class Parent {
    static {
        System.out.println("Parent initialized");
    }
}

class Child extends Parent {
    static {
        System.out.println("Child initialized");
    }
}

public class Main {
    public static void main(String[] args) {
        Child child = new Child(); // 输出：Parent initialized, Child initialized
    }
}
```

### Java 虚拟机启动时
包含main方法的类在虚拟机启动时会被初始化。例如：

```plain
public class Main {
    static {
        System.out.println("Main class initialized");
    }

    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

## 被动引用
### 通过子类引用父类的静态字段
通过子类引用父类的静态字段，不会导致子类初始化。

```plain
class Parent {
    static int value = 42;
}

class Child extends Parent {
    static {
        System.out.println("Child initialized");
    }
}

public class Main {
    public static void main(String[] args) {
        System.out.println(Child.value); // 输出：42，不会触发 Child 的初始化
    }
}
```

### 定义对象数组
定义类的对象数组不会触发类的初始化。例如：

```plain
MyClass[] array = newMyClass[10]; // 不会触发 MyClass 的初始化
```

### 常量引用
引用常量不会触发类的初始化，因为常量在编译阶段会存入调用类的常量池中。例如：

```plain
class MyClass {
    static final int CONSTANT = 42;
}

public class Main {
    public static void main(String[] args) {
        System.out.println(MyClass.CONSTANT); // 不会触发 MyClass 的初始化
    }
}
```
