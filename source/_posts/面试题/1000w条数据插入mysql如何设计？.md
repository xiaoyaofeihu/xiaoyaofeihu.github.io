---
title: 1000w条数据插入mysql如何设计？
date: 2025-02-15 13:33:41
tags:
	- 场景题
categories: 面试
---
# 👌1000w条数据插入mysql如何设计？

### 1. 使用批量插入（Bulk Insert）
批量插入可以显著减少 SQL 语句的开销和网络传输的负担。可以使用`INSERT INTO ... VALUES`语句一次插入多条记录。

#### 示例：
```plain
INSERT INTO your_table (column1, column2, column3) VALUES
('value1', 'value2', 'value3'),
('value4', 'value5', 'value6'),
...
('valueN1', 'valueN2', 'valueN3');
```

### 2. 禁用或延迟索引和约束（不到万不得已，别搞，不推荐）
在插入大量数据之前，可以暂时禁用或延迟索引和外键约束，以减少插入过程中的开销。插入完成后再重新启用索引和约束。

#### 禁用外键检查：
```plain
SET foreign_key_checks =0;
```

#### 禁用唯一检查：
```plain
SET unique_checks =0;
```

### 3. 调整 MySQL 配置
确保 MySQL 配置足够支持大批量数据插入。

#### 增大缓冲区大小：
```plain
[mysqld]
innodb_buffer_pool_size = 1G  # 根据你的内存大小调整
innodb_log_buffer_size = 64M
```

#### 调整事务日志大小：
```plain
[mysqld]
innodb_log_file_size = 512M
innodb_log_files_in_group = 2
```

### 4. 使用事务
将大量插入操作分批放入事务中，可以减少每次插入的提交开销。如果数据量非常大，可以每 1000 或 10000 条记录提交一次。换成代码使用就是 transactiontemplate来手动控制事务提交

#### 示例：
```plain
START TRANSACTION;

INSERT INTO your_table (column1, column2, column3) VALUES
('value1', 'value2', 'value3'),
('value4', 'value5', 'value6'),
...
('value1000', 'value1001', 'value1002');

COMMIT;
```

### 5. 使用分区表
如果数据量非常大且有分区需求，可以考虑使用 MySQL 的分区表功能，将数据按某个字段（如日期、ID 等）进行分区，提升查询和插入性能。

### 6. 数据导入工具
对于非常大的数据集，可以使用 MySQL 提供的工具如`mysqlimport`或者第三方工具如`mydumper/myloader`进行数据导入，这些工具通常针对大数据集做了优化。
