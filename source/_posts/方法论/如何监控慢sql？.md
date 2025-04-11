---
title: 👌如何监控慢sql？
date: 2025-04-09 13:33:41
categories: 方法论
---

# 👌如何监控慢sql？
### 1. 启用慢查询日志
慢查询日志是 MySQL 内置的一种功能，用于记录执行时间超过指定阈值的 SQL 查询。

#### 配置 MySQL 配置文件（my.cnf 或 my.ini）
在 MySQL 配置文件中添加或修改以下参数：

```plain
[mysqld]
slow_query_log = 1
slow_query_log_file = /var/log/mysql/slow-query.log
long_query_time = 2
log_queries_not_using_indexes = 1
```

+ slow_query_log：启用慢查询日志。
+ slow_query_log_file：指定慢查询日志文件的路径。
+ long_query_time：定义慢查询的阈值（单位：秒）。
+ log_queries_not_using_indexes：记录未使用索引的查询。

#### 重启 MySQL 服务
修改配置文件后，重启 MySQL 服务以使配置生效：

```plain
sudo systemctl restart mysql
```

### 2. 使用 MySQL 内置的性能模式（Performance Schema）
Performance Schema 是 MySQL 内置的一个工具，用于收集数据库内部的运行时统计信息。可以通过以下步骤启用和使用 Performance Schema：

#### 启用 Performance Schema
在 MySQL 配置文件中添加或修改以下参数：

```plain
[mysqld]
performance_schema = ON
```

#### 重启 MySQL 服务
修改配置文件后，重启 MySQL 服务以使配置生效：

```plain
sudo systemctl restart mysql
```

#### 查询慢查询信息
使用以下 SQL 语句查询慢查询信息：

```plain
SELECT*FROM performance_schema.events_statements_summary_by_digestORDERBY SUM_TIMER_WAIT DESC
LIMIT 10;
```

### 3. 使用 MySQL 企业监控工具
MySQL 提供了企业版的监控工具，如 MySQL Enterprise Monitor，它可以自动收集、分析和报告慢查询信息。这个工具适合企业环境下的全面监控和管理。

### 4. 使用第三方监控工具
有许多第三方监控工具可以帮助你监控 MySQL 的性能，包括慢查询。这些工具通常提供更丰富的功能和更友好的界面。常见的第三方工具包括：

+ **Percona Monitoring and Management (PMM)**：一个开源的监控和管理工具，专为 MySQL 和 MongoDB 设计。
+ **New Relic**：一个强大的应用性能监控工具，支持 MySQL 和其他数据库。
+ **Datadog**：一个综合性的监控平台，支持 MySQL 和其他数据库。

### 5. 使用 SQL 分析工具
MySQL 提供了EXPLAIN语句，用于分析 SQL 查询的执行计划。通过分析执行计划，可以识别和优化慢查询。

#### 使用EXPLAIN分析查询
```plain
EXPLAIN SELECT*FROM your_table WHERE your_condition;
```

EXPLAIN的输出结果包含了查询执行的详细信息，包括使用的索引、扫描的行数等。通过分析这些信息，可以找出查询的性能瓶颈并进行优化。

### 总结
监控慢 SQL 是一个持续的过程，需要结合多种工具和方法。以下是一个综合的监控策略：

1. **启用慢查询日志**：记录执行时间超过阈值的查询。
2. **使用 Performance Schema**：收集和分析详细的运行时统计信息。
3. **使用企业监控工具**：如 MySQL Enterprise Monitor，进行全面的监控和管理。
4. **使用第三方监控工具**：如 Percona Monitoring and Management (PMM)、New Relic、Datadog 等。
5. **使用 SQL 分析工具**：如EXPLAIN，分析和优化查询执行计划。