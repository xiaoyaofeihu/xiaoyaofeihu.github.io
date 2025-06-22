---
title: redis滑动窗口限流怎么做的
date: 2025-02-15 13:33:41
tags:
	- 场景题
categories: 面试
---
# 👌Redis滑动窗口限流怎么做的说一下？

Redis的滑动窗口限流是一种基于时间窗口的限流策略，用于控制在特定时间范围内的请求数量。其基本思想是将时间划分为多个小的子窗口，通过记录每个子窗口内的请求数量来实现限流。与固定窗口限流相比，滑动窗口限流可以更平滑地限制请求速率，减少突发流量对系统的影响。

### 基本概念
1. **时间窗口**：滑动窗口限流将时间划分为多个小的子窗口。例如，要限制每秒最多10个请求，可以将1秒划分为10个子窗口，每个子窗口100毫秒。
2. **记录请求时间戳**：使用Redis的有序集合（Sorted Set）记录请求的时间戳。
3. **计算当前窗口内的请求数量**：每次有新请求到来时，移除过期的时间戳，计算当前窗口内的请求数量。
4. **判断是否限流**：如果当前窗口内的请求数量超过限流阈值，则拒绝请求；否则，允许请求并记录时间戳。

### 实现步骤
1. **确定时间窗口和子窗口大小**。
2. **使用Redis有序集合记录请求时间戳**。
3. **计算当前窗口内的请求数量**。
4. **判断是否限流**。

### 示例
```plain
import redis.clients.jedis.Jedis;
import redis.clients.jedis.Transaction;

import java.util.List;

public class SlidingWindowRateLimiter {
    private Jedis jedis;
    private String key;
    private int windowSizeInMillis;
    private int limit;

    public SlidingWindowRateLimiter(Jedis jedis, String key, int windowSizeInMillis, int limit) {
        this.jedis = jedis;
        this.key = key;
        this.windowSizeInMillis = windowSizeInMillis;
        this.limit = limit;
    }

    public boolean isAllowed() {
        long currentTimeMillis = System.currentTimeMillis();
        long windowStart = currentTimeMillis - windowSizeInMillis;

        Transaction transaction = jedis.multi();
        transaction.zremrangeByScore(key, 0, windowStart); // 移除过期的时间戳
        transaction.zcard(key); // 获取当前窗口内的请求数量
        transaction.zadd(key, currentTimeMillis, String.valueOf(currentTimeMillis)); // 添加当前请求的时间戳
        transaction.expire(key, windowSizeInMillis / 1000 * 2); // 设置过期时间
        List<Object> results = transaction.exec();

        long currentCount = (Long) results.get(1); // 获取当前窗口内的请求数量

        return currentCount <= limit;
    }

    public static void main(String[] args) {
        Jedis jedis = new Jedis("localhost", 6379);
        SlidingWindowRateLimiter limiter = new SlidingWindowRateLimiter(jedis, "rate_limiter_key", 1000, 10);

        if (limiter.isAllowed()) {
            System.out.println("请求被允许");
        } else {
            System.out.println("请求被拒绝");
        }

        jedis.close();
    }
}
```

### 详细解释
1. **初始化**：
    - `SlidingWindowRateLimiter`类的构造函数接受`jedis`（Jedis客户端实例）、`key`（Redis键）、`windowSizeInMillis`（时间窗口大小，以毫秒为单位）和`limit`（限流阈值）。
2. **isAllowed 方法**：
    - 获取当前时间戳（毫秒）。
    - 计算窗口开始时间（当前时间减去窗口大小）。
    - 使用Redis事务（`Transaction`）执行以下操作：
        * `zremrangeByScore`：移除有序集合中所有时间戳小于窗口开始时间的元素（即过期的时间戳）。
        * `zcard`：获取当前窗口内的请求数量。
        * `zadd`：添加当前请求的时间戳到有序集合中。
        * `expire`：设置Redis键的过期时间，防止长期不活跃时占用Redis内存。
    - 执行事务并获取结果。
    - 检查当前窗口内的请求数量是否超过限流阈值，决定是否允许请求。
3. **main 方法**：
    - 创建Jedis客户端实例并连接到Redis服务器。
    - 创建`SlidingWindowRateLimiter`实例并调用`isAllowed`方法来检查请求是否被允许。
    - 关闭Jedis客户端。

### 优点
+ **平滑限流**：滑动窗口限流相比固定窗口限流更加平滑，能够更好地应对突发流量。
+ **精确控制**：可以精确控制特定时间窗口内的请求数量。

### 缺点
+ **复杂度较高**：实现相对复杂，需要精确管理时间戳和窗口。
+ **性能开销**：频繁的Redis操作可能会带来一定的性能开销。