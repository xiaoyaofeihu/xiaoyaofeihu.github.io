---
title: Mysql事务 
date: 2024-11-10 17:33:00
tags:
	- Mysql
categories: 数据库
---

## 什么是索引下推
### 官方索引条件下推： 
>Index Condition Pushdown，简称ICP。是MySQL5.6对使用索引从表中检索行的
一种优化。ICP可以减少存储引擎必须访问基表的次数以及MySQL服务器必须访问存储引擎的次数。可
用于 InnoDB 和 MyISAM 表，对于InnoDB表ICP仅用于辅助索引。
可以通过参数optimizer_switch控制ICP的开始和关闭。


### 索引下推和覆盖索引是减少回表的一种手段
+ ICP可以有效减少回表查询次数和返回给服务层的记录数，从而
减少了磁盘IO次数和服务层与存储引擎的交互次数。


### 问题
以InnoDB的辅助索引为例，来讲解ICP的作用：MySQl在使用组合索引在检索数据时是使用最左前缀原
则来定位记录，左侧前缀之后不匹配的后缀，MySQL会怎么处理？

### 配置
#optimizer_switch优化相关参数开关
mysql> show VARIABLES like 'optimizer_switch';
#关闭ICP
SET optimizer_switch = 'index_condition_pushdown=off';
#开启ICP
SET optimizer_switch = 'index_condition_pushdown=on';

### 示例
```
CREATE TABLE `test1`  (
  `id` int NOT NULL,
  `id1` int NULL DEFAULT NULL,
  `id2` int NULL DEFAULT NULL,
  `id3` int NULL DEFAULT NULL,
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NULL DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE,
  INDEX `id1`(`id1`, `id2`, `id3`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_0900_ai_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of test1
-- ----------------------------
INSERT INTO `test1` VALUES (1, 2, 3, 4, 'zhangsan ');
INSERT INTO `test1` VALUES (2, 2, 3, 5, 'lisi');
INSERT INTO `test1` VALUES (3, 2, 4, 5, 'wangwu ');

SET FOREIGN_KEY_CHECKS = 1;
``` 
```

EXPLAIN SELECT * FROM `test1` WHERE id1 = 1 AND id2 > 1 AND id3 = 3;
```
如果使用了索引下推，Extra 中是Using index condition


### 注意！！！

如果你的 EXPLAIN 输出显示：
```
Extra: Using where; Using index
Using where：表示部分条件是在 MySQL 层处理的，而不是完全依赖索引。
Using index：说明查询是覆盖索引的（即所有查询的数据都可以从索引中获取，避免回表）。
```
这说明，虽然联合索引 (id1, id2, id3) 被使用，但优化器可能认为使用 索引覆盖扫描 比触发索引下推更高效。

