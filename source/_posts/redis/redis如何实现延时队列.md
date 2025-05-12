---
title: 👌redis如何实现延迟队列？
date: 2025-05-09 13:33:41
tags:
	- 缓存
categories: Redis
---


# 👌redis如何实现延时队列

# 题目详细答案
可以使用有序集合（Sorted Set）来实现延时队列。有序集合中的每个元素有一个关联的分数，可以用来表示任务的执行时间戳。具体的步骤如下，非常简单

## 添加任务到延时队列
将任务添加到有序集合中，使用任务的执行时间作为分数（score）。

```plain
// 示例代码：添加任务到延时队列
String queueName = "delay_queue";
String taskId="task_1";
long delay=5000; // 延迟时间（毫秒）
long executionTime= System.currentTimeMillis() + delay;
Jedis jedis = newJedis("localhost");
jedis.zadd(queueName, executionTime, taskId);
jedis.close();
```

## 轮询延时队列并执行任务
定期检查有序集合中的任务，找到那些执行时间已经到达或超过当前时间的任务，并执行这些任务。

```plain
// 示例代码：轮询延时队列并执行任务
String queueName = "delay_queue";
Jedis jedis=new Jedis("localhost");
while (true) {
    long currentTime= System.currentTimeMillis();
    Set<Tuple> tasks = jedis.zrangeByScoreWithScores(queueName, 0, currentTime, 0, 1);

    if (tasks.isEmpty()) {
        // 没有任务需要执行，休眠一段时间
        Thread.sleep(1000);
        continue;
    }

    for (Tuple task : tasks) {
        StringtaskId= task.getElement();
        // 执行任务
        executeTask(taskId);

        // 从队列中移除已执行的任务
        jedis.zrem(queueName, taskId);
    }
}

jedis.close();
private static void executeTask(String taskId) {
    // 实现任务执行逻辑
    System.out.println("Executing task: " + taskId);
}
```