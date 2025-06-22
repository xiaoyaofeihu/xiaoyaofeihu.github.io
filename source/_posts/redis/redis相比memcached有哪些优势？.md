---
title: 👌redis相比memcached有哪些优势？
date: 2025-05-10 13:33:41
tags:
	- 缓存
categories: Redis
---

# 👌redis相比memcached有哪些优势？


# 口语化回答
redis 相比 memcached，主要提供了丰富的数据类型和各种高级操作。memcached 仅仅支持字符串类型，redis 支持 5 种基础+4 种高级，非常丰富。还有就是 redis 提供了 rdb 和 aof 的持久化机制，memcached 一旦重启，数据就会直接丢失。分布式相关，redis 天然支持主从复制，哨兵，集群，memcached 还需要靠一些第三方库和工具类实现。再高级一点的就是 redis 支持像 lua，事务，发布订阅这些，memcached 统统不支持。

# 题目解析
最近几年基本上不会考这道题了，这道题在早些时候，memcached 还用的时候，问的比较多。除非碰到还在用memcached 的公司，否则不会被问到。大家稍微了解即可，比较的过程也是了解历史的过程。

# 面试得分点
丰富数据类型，持久化，分布式，lua，事务

# 题目详细答案
通过一个表格来进行对比

| | redis | Memcached |
| --- | --- | --- |
| 数据类型 | Redis 支持字符串、哈希、列表、集合、有序集合、位图、HyperLogLog、地理空间和流等多种数据类型 | Memcached 仅支持字符串类型的键值对。 |
| 持久化机制 | Redis 支持将数据持久化到磁盘，可以通过 RDB 快照和 AOF 日志来保存数据，确保在系统重启后数据不会丢失 | Memcached不支持持久化，数据仅保存在内存中，一旦服务器重启或宕机，数据就会丢失。 |
| 分布式 | 支持主从，哨兵，集群 | Memcached也支持分布式部署，但通常需要依赖第三方库或工具来实现 |
| 脚本功能 | Redis 支持在服务器端执行 Lua 脚本，能够将多个操作合并为一个原子操作 | 不支持 |
| 事务 | Redis 提供 MULTI、EXEC、WATCH 等命令，支持简单的事务操作 | 不支持事务 |
| 内存淘汰策略 | Redis 提供多种内存淘汰策略，如 LRU、LFU 等 | Memcached 仅支持 LRU 淘汰策略。 |
