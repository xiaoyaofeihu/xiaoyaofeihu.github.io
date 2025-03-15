---
title: 👌Rabbitmq如何避免消息丢失？
date: 2025-03-02 13:33:41
tags:
	- Mysql
categories: 面试
---

# 👌Rabbitmq如何避免消息丢失？

# 口语化回答
好的，面试官，rabbitmq 主要通过持久化机制，ack 确认机制，镜像等保证消息不丢失。rabbitmq 在创建队列的时候，可以设置为持久化队列，消息也可以设置为持久化消息，这样消息会被写入磁盘。即使重启，也不会产生丢失的问题。确认机制是我们平时最常见的机制，ack，消费者正常消费成功过消息后，会发出 ack 信号。mq 收到之后，才会认为消息成功消费，否则会认为消费失败，此时不会抛弃会让其他消费者继续进行消费。镜像队列是高可用的一种形式。可以确保一个节点挂了，另一个节点可以正常的存储消息，类似 redis 主从复制。以上。


## 消息持久化
**1、 持久化队列**：创建队列时，可以将其声明为持久化队列（durable）。持久化队列在 RabbitMQ 重启后依然存在。

```plain
channel.queueDeclare("queue_name", true, false, false, null);
```

**2、 持久化消息**：在发送消息时，可以将消息标记为持久化（persistent）。持久化消息会被写入磁盘，即使 RabbitMQ 重启，消息也不会丢失。

```plain
AMQP.BasicProperties props = new AMQP.BasicProperties.Builder()
    .deliveryMode(2) // 2 表示持久化消息
    .build();
channel.basicPublish("exchange_name", "routing_key", props, messageBody);
```

## 消息确认机制（Acknowledgements）
**1、 消费者确认**：消费者处理完消息后，向 RabbitMQ 发送确认（ack）。如果消费者未确认消息（如消费者崩溃），RabbitMQ 会将消息重新投递给其他消费者。

```plain
// 自动确认关闭
channel.basicConsume("queue_name", false, consumer);

// 消费者处理完消息后手动确认
channel.basicAck(deliveryTag, false);
```

**2、 发布者确认**：发布者可以启用发布确认模式（publisher confirms），RabbitMQ 会在消息成功写入队列后发送确认给发布者。

```plain
channel.confirmSelect();
channel.basicPublish("exchange_name", "routing_key", null, messageBody);
channel.waitForConfirmsOrDie(); // 等待确认
```

## 事务支持
RabbitMQ 支持 AMQP 事务，可以在一个事务中发布多条消息，确保这些消息要么全部成功，要么全部失败。

```plain
channel.txSelect();
channel.basicPublish("exchange_name", "routing_key", null, messageBody);
channel.txCommit(); // 提交事务
```

## 镜像队列（Mirrored Queues）
RabbitMQ 的集群模式支持镜像队列（也称为高可用队列），可以将队列的数据复制到多个节点上，确保在一个节点故障时，其他节点仍然可以提供服务。

```plain
// 定义队列策略，将队列配置为镜像队列
rabbitmqctl set_policy ha-all "^queue_name$" '{"ha-mode":"all"}'
```

## 消费者重试机制
消费者可以实现重试机制，当消息处理失败时，可以将消息重新投递到队列或死信队列，避免消息丢失。

```plain
try {
    // 处理消息
    channel.basicAck(deliveryTag, false);
} catch (Exception e) {
    channel.basicNack(deliveryTag, false, true); // 重试
}
```