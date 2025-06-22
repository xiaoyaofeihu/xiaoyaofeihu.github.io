---
title: 👌使用redis统计网站的uv，应该怎么做
date: 2025-05-06 13:33:41
tags:
	- 缓存
categories: Redis
---

# 👌使用redis统计网站的uv，应该怎么做


# 题目详细答案
常见的是使用Set数据结构和HyperLogLog数据结构。

## 使用Set统计UV
Set是一种集合数据结构，可以存储不重复的元素。将每个访客的唯一标识（如用户ID或IP地址）添加到Set中，可以很方便地统计独立访客数。

1. **记录访客访问**：每次有访客访问时，将其唯一标识添加到当天的Set中。
2. **获取UV**：使用SCARD命令获取Set中元素的数量，即为独立访客数。

```plain
import redis.clients.jedis.Jedis;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
public class UVTrackerSet {
    private Jedis jedis;
    private static final DateTimeFormatter DATE_FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd");

    public UVTrackerSet(String redisHost, int redisPort) {
        this.jedis = newJedis(redisHost, redisPort);
    }

    public void recordVisit(String userId) {
        String date= LocalDate.now().format(DATE_FORMATTER);
        String key="uv:set:" + date;
        jedis.sadd(key, userId);
        // 设置键的过期时间为30天，防止内存无限增长
        jedis.expire(key, 30 * 24 * 60 * 60);
    }

    public long getUV(String date) {
        String key="uv:set:" + date;
        return jedis.scard(key);
    }

    public long getUVRange(String startDate, String endDate) {
        LocalDatestart= LocalDate.parse(startDate, DATE_FORMATTER);
        LocalDateend= LocalDate.parse(endDate, DATE_FORMATTER);

        String[] keys = start.datesUntil(end.plusDays(1))
                .map(date -> "uv:set:" + date.format(DATE_FORMATTER))
                .toArray(String[]::new);

        StringtempKey="uv:set:range";
        jedis.sunionstore(tempKey, keys);
        longuvCount= jedis.scard(tempKey);
        jedis.del(tempKey);
        return uvCount;
    }

    public static void main(String[] args) {
        UVTrackerSet tracker = new UVTrackerSet("localhost", 6379);

        // 记录访客访问
        tracker.recordVisit("user_123");
        tracker.recordVisit("user_456");

        // 获取指定日期的UV
        Stringtoday= LocalDate.now().format(DATE_FORMATTER);
        System.out.println("UV for " + today + ": " + tracker.getUV(today));

        // 获取一段时间内的UV
        StringstartDate="2023-07-01";
        StringendDate="2023-07-07";
        System.out.println("UV from " + startDate + " to " + endDate + ": " + tracker.getUVRange(startDate, endDate));
    }
}
```

## 使用HyperLogLog统计UV
HyperLogLog是一种概率性数据结构，可以在固定的内存空间内提供高效的基数估计。它适合处理大规模数据。

1. **记录访客访问**：每次有访客访问时，将其唯一标识添加到当天的HyperLogLog中。
2. **获取UV**：使用PFCOUNT命令获取HyperLogLog的基数估计。

```plain
import redis.clients.jedis.Jedis;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
public class UVTrackerHLL {

    private Jedis jedis;
    private static final DateTimeFormatter DATE_FORMATTER= DateTimeFormatter.ofPattern("yyyy-MM-dd");

    public UVTrackerHLL(String redisHost, int redisPort) {
        this.jedis = newJedis(redisHost, redisPort);
    }

    public void recordVisit(String userId) {
        Stringdate= LocalDate.now().format(DATE_FORMATTER);
        Stringkey="uv:hll:" + date;
        jedis.pfadd(key, userId);
        // 设置键的过期时间为30天，防止内存无限增长
        jedis.expire(key, 30 * 24 * 60 * 60);
    }

    public long getUV(String date) {
        Stringkey="uv:hll:" + date;
        return jedis.pfcount(key);
    }

    public long getUVRange(String startDate, String endDate) {
        LocalDatestart= LocalDate.parse(startDate, DATE_FORMATTER);
        LocalDateend= LocalDate.parse(endDate, DATE_FORMATTER);

        String[] keys = start.datesUntil(end.plusDays(1))
                .map(date -> "uv:hll:" + date.format(DATE_FORMATTER))
                .toArray(String[]::new);

        StringtempKey="uv:hll:range";
        jedis.pfmerge(tempKey, keys);
        longuvCount= jedis.pfcount(tempKey);
        jedis.del(tempKey);
        return uvCount;
    }

    public static void main(String[] args) {
        UVTrackerHLLtracker=newUVTrackerHLL("localhost", 6379);

        // 记录访客访问
        tracker.recordVisit("user_123");
        tracker.recordVisit("user_456");

        // 获取指定日期的UV
        Stringtoday= LocalDate.now().format(DATE_FORMATTER);
        System.out.println("UV for " + today + ": " + tracker.getUV(today));

        // 获取一段时间内的UV
        StringstartDate="2023-07-01";
        StringendDate="2023-07-07";
        System.out.println("UV from " + startDate + " to " + endDate + ": " + tracker.getUVRange(startDate, endDate));
    }
}
```

## 优缺点对比
| | set | hyperloglog |
| --- | --- | --- |
| 精准度 | 精确统计，无误差 | 存在一定误差（通常在0.81%左右） |
| 占用内存 | 内存占用较大，尤其是当访客数量很大时 | 内存占用小，通常只需要12KB内存。 |
| 内存占用情况 | 小数据量，同时对内存不敏感可以 | 适合大规模数据 |
