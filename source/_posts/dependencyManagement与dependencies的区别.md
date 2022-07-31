---
title: dependencyManagement与dependencies的区别
date: 2022-07-31 13:33:41
tags:
	- java
categories: 笔记
---


+ dependencyManagement
    + 用于父类的管理，一般写在顶层父类的pom.xml中
    + 只是做声明依赖，不做具体引入，只有在子项目中使用到是才会实现依赖
    + 子类声明了version，就用子类自己的，否则都继承父类的version和scope
+ dependencies
    + 默认被子类全部继承         