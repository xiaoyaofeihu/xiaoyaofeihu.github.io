---
title: 👌一次完整的垃圾回收过程是什么样的？
date: 2025-04-27 13:33:41
tags:
	- JVM
categories: 笔记
--- 
# 👌Java的类加载器有哪些?

# 题目详细答案
在 Java 中，类加载器（ClassLoader）是负责将类文件加载到 JVM 中的组件。Java 提供了几种标准的类加载器，每种类加载器都有特定的职责和加载范围。

## 启动类加载器（Bootstrap ClassLoader）
**职责**：加载 Java 核心类库，如java.lang.*、java.util.*等。

**实现**：由本地代码（通常是 C++）实现，不是java.lang.ClassLoader的子类。

**加载路径**：$JAVA_HOME/jre/lib/rt.jar或jrt:/modules（在模块化系统中）。

**特点**：是所有类加载器的顶层，没有父类加载器。

## 扩展类加载器（Extension ClassLoader）
**职责**：加载扩展库中的类。

**实现**：由sun.misc.Launcher$ExtClassLoader实现，是java.lang.ClassLoader的子类。

**加载路径**：$JAVA_HOME/jre/lib/ext目录或由java.ext.dirs系统属性指定的目录。

**父类加载器**：引导类加载器。

## 应用程序类加载器（Application ClassLoader）
**职责**：加载应用程序类路径（classpath）中的类。

**实现**：由sun.misc.Launcher$AppClassLoader实现，是java.lang.ClassLoader的子类。

**加载路径**：由java.class.path系统属性指定的目录和 JAR 文件。

**父类加载器**：扩展类加载器。

## 自定义类加载器（Custom ClassLoader）
**职责**：满足特定需求的类加载器，通常在应用程序中自定义实现。

**实现**：继承java.lang.ClassLoader并重写findClass方法。

**加载路径**：由开发者自行定义，可以是文件系统、网络、数据库等。

**父类加载器**：可以指定，也可以继承应用程序类加载器。

以下是一个简单的自定义类加载器示例：

```plain
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public class MyClassLoader extends ClassLoader {

    private String classPath;

    public MyClassLoader(String classPath) {
        this.classPath = classPath;
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            // 将类名转换为文件路径
            String fileName = classPath + name.replace('.', '/') + ".class";
            // 读取类文件的字节码
            byte[] classBytes = Files.readAllBytes(Paths.get(fileName));
            // 将字节码转换为 Class 对象
            return defineClass(name, classBytes, 0, classBytes.length);
        } catch (IOException e) {
            throw new ClassNotFoundException(name, e);
        }
    }
}
```



> 原文: <https://www.yuque.com/jingdianjichi/xyxdsi/aldxe3g9bqszh1eh>