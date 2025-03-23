---
title: SpringSecurity+ oauth2实现同账号多端同时登录 
date: 2022-07-31 13:33:41
tags:
	- 权限
categories: java
---

# SpringSecurity+ oauth2实现同账号多端同时登录 
参考链接：https://blog.csdn.net/m0_67391377/article/details/126509478
    单点登录：https://mp.weixin.qq.com/s/DGFFPl93kZxS5G_DSFTBDA
    多端登录：https://blog.csdn.net/zhourenfei17/article/details/88826911


# shiro中的Realm如何使用
参考链接：https://blog.csdn.net/m0_54849873/article/details/124345270
# shiro域（安全数据源）
    shiro从Realm获取安全的数据（例如用户，角色，权限等）
    SecurityManager需要进行身份验证就必须从Realm中获取到一个合法的用户身份，从而比较用户身份是否合法，同时在SecurityManager获取身份的同时Realm也需要维护一套用户身份用来判断用户是否能执行某项操作