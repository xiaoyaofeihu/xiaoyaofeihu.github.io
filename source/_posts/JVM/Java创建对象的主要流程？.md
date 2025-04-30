---
title: 👌Java创建对象的主要流程？
date: 2025-04-27 13:33:41
tags:
	- JVM
categories: 笔记
--- 
# 👌Java创建对象的主要流程？

# 题目详细答案
## 类加载检查
当使用new关键字创建对象时，JVM 首先检查类是否已经被加载、链接和初始化。如果类还没有被加载，JVM 会先执行类加载过程。类加载过程包括以下步骤：

**加载（Loading）**：从文件系统或网络中读取类的二进制数据，并创建一个Class对象。

**链接（Linking）**：包括验证（Verify）、准备（Prepare）和解析（Resolve）三个阶段。

+ **验证**：确保类的字节码符合 JVM 规范，没有安全问题。
+ **准备**：为类的静态变量分配内存，并将其初始化为默认值。
+ **解析**：将类的符号引用转换为直接引用。

**初始化（Initialization）**：执行类的静态初始化块和静态变量的初始化。

## 内存分配
一旦类被加载并初始化，JVM 会在堆中为新对象分配内存。内存分配的具体方式取决于 JVM 的实现，一般有以下几种方式：

**指针碰撞（Bump-the-pointer）**：如果堆内存是规整的，所有已使用的内存都在一边，空闲内存都在另一边，中间有一个指针作为分界线。分配内存时，只需将指针向空闲内存方向移动一段与对象大小相等的距离。

**空闲列表（Free-list）**：如果堆内存不规整，JVM 会维护一个空闲列表，记录哪些内存块是可用的。分配内存时，从空闲列表中找到一个足够大的内存块进行分配。

## 初始化零值
在内存分配完成后，JVM 会将分配的内存空间初始化为零值。这一步确保了对象的实例变量在 Java 语言层面上有默认值（如0、false或null）。

## 设置对象头
JVM 会在对象的内存区域中设置对象头（Object Header），对象头包含以下信息：

**Mark Word**：存储对象的运行时数据，如哈希码、GC 分代年龄、锁状态等。

**Class Pointer**：指向对象的类元数据，JVM 通过它来确定对象是哪个类的实例。

## 执行构造器
最后，JVM 调用对象的构造器（Constructor）进行初始化。构造器初始化包括：

**执行父类构造器**：如果类有父类，首先会调用父类的构造器。

**初始化实例变量**：按照代码中定义的顺序初始化实例变量。

**执行构造器代码**：执行构造器中的代码。

## 返回对象引用
构造器执行完毕后，JVM 返回新创建对象的引用。此时，对象已经完全初始化，可以被程序使用。

### 示例代码
展示对象创建的流程：

```plain
public class MyClass {
    private int value;

    // 静态初始化块
    static {
        System.out.println("Class MyClass is being initialized.");
    }

    // 实例初始化块
    {
        value = 10;
        System.out.println("Instance initialization block.");
    }

    // 构造器
    public MyClass() {
        System.out.println("Constructor is called.");
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        System.out.println("Object created with value: " + obj.value);
    }
}
```

### 输出
```plain
Class MyClass is being initialized.
Instance initialization block.
Constructor is called.
Object created with value: 10
```

### 