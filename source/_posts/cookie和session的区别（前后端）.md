---
title: cookie和session的区别
date: 2022-07-31 13:33:41
tags:
	- 存储
categories: java
---

# cookie 和 session 的区别
# localstorage 和cookie 的区别
+ 都可以用来做本地存储，实现数据的持久化,区别如下
    + localstroage存储的内容会多一点，5M；cookie存储的只有4k；
    + 有效时间不一样；cookie 的有效时间可以自行设置，local可以一直生效；
    + 请求时cookie可以被携带，同源的cookie信息会自动作为请求头的一部分发给服务端；local不会，一直存储在浏览器端
# localstroage 和sessionstroage 的区别
+ 都是前端的本地存储（保存在客户端，不与服务器进行交互通信，存储数据的大小一样，只能存储字符串类型的数据）
    + 生命周期不同，local是永久的，除非主动删除；session的生命周期仅在当前会话下有效，在同源的窗口中始终存在的数据（只要浏览器的窗口没有关闭，即使刷新页面或者进入同源的另一个页面数据依旧存在，但在关闭浏览器窗口就会被销毁）
# session 和sessionStroage 的区别
+ session主要的作用是维持会话状态的key，sessionStroage则是存储会话期间的数据