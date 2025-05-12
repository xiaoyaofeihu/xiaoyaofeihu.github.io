---
title: 👌redis的内存用完了会发生什么？
date: 2025-05-06 13:33:41
tags:
	- 缓存
categories: Redis
---

# 👌redis的内存用完了会发生什么？

redis 内存用完之后发生的现象主要取决于我们配置的内存回收策略。默认是noeviction，这个策略不会删除任何的键，当内存不足的时候，就会报错。这种策略，我们一般不使用。常见使用的就是 lru，回收最近最少使用的有过期时间的键。其他的策略还比如 randow，可以回收随机的键。ttl 按照最短的过期时间来进行回收。以上。


# 面试得分点
lru，lfu，random，无过期

# 题目详细答案
当Redis的内存用完时，会根据配置的内存回收策略采取不同的措施。可以在内存达到限制时决定如何处理新的写请求。主要的策略有如下 8 种。

### 内存回收策略
1. **noeviction**：不删除任何键，当内存不足时返回错误。这是默认策略。

当内存达到限制时，Redis将不再接受任何写请求，并返回错误。例如，客户端尝试设置新键时，会收到类似以下的错误信息：

```plain
(error) OOM command not allowed when used memory > 'maxmemory'.
```

2. **allkeys-lru**：使用最近最少使用（LRU）算法回收所有键。
3. **volatile-lru**：使用最近最少使用（LRU）算法回收设置了过期时间的键。

Redis将根据LRU算法选择最近最少使用的键进行删除，以腾出空间存储新的数据。allkeys-lru会在所有键中选择，volatile-lru只会在设置了过期时间的键中选择。

4. **allkeys-random**：随机回收所有键。
5. **volatile-random**：随机回收设置了过期时间的键。

Redis会随机选择一些键进行删除，以腾出空间。allkeys-random会在所有键中选择，volatile-random只会在设置了过期时间的键中选择。

6. **volatile-ttl**：回收那些剩余生存时间（TTL）最短的键。

Redis将选择那些剩余生存时间（TTL）最短的键进行删除。

7. **volatile-lfu**：使用最长时间没有被使用（LFU）算法回收设置了过期时间的键。
8. **allkeys-lfu**：使用最长时间没有被使用（LFU）算法回收所有键。

Redis将根据LFU算法选择最近最少使用的键进行删除。volatile-lfu只会在设置了过期时间的键中选择，allkeys-lfu会在所有键中选择。

### 配置内存回收策略的方式
redis.conf文件中配置内存回收策略，例如：

```plain
maxmemory 100mb
maxmemory-policy allkeys-lru
```

也可通过命令行参数设置：

```plain
redis-server --maxmemory 100mb --maxmemory-policy allkeys-lru
```