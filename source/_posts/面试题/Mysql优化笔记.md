---
title: 在 SQL 查询中，WHERE 和 HAVING 都可以用来过滤数据
date: 2024-09-16 13:33:41
tags:
	-  Mysql
categories: 面试
---

在 SQL 查询中，WHERE 和 HAVING 都可以用来过滤数据，但它们的工作机制不同：

WHERE：在分组（GROUP BY）之前过滤数据，属于预过滤。
HAVING：在分组（GROUP BY）之后过滤数据，属于后过滤。
将适合放在 WHERE 条件的过滤写在 HAVING 中，可能导致大量不必要的数据参与分组运算，从而增加查询的时间和资源消耗。

示例
原始表
假设有一个销售记录表 sales：

sql
复制代码
CREATE TABLE sales (
    id INT PRIMARY KEY,
    product_id INT,
    sale_date DATE,
    amount DECIMAL(10, 2)
);
数据样例：

id	product_id	sale_date	amount
1	101	2024-01-01	100.00
2	102	2024-01-02	200.00
3	101	2024-01-03	300.00
4	103	2024-01-04	400.00
5	101	2024-01-05	500.00
查询需求
我们需要统计每个产品的总销售金额大于 500 的记录。

错误示例：使用 HAVING
sql
复制代码
SELECT product_id, SUM(amount) AS total_amount
FROM sales
GROUP BY product_id
HAVING product_id = 101 AND total_amount > 500;
问题：

HAVING product_id = 101 是一个简单的等值条件，但它放在 HAVING 中，会导致所有数据都参与 GROUP BY 和 SUM 运算。
这增加了计算负担，尤其是当数据量很大时。
优化：使用 WHERE
sql
复制代码
```sql
SELECT product_id, SUM(amount) AS total_amount
FROM sales
WHERE product_id = 101
GROUP BY product_id
HAVING total_amount > 500;
```
改进：

在 WHERE 中预先过滤出 product_id = 101 的数据，减少了参与 GROUP BY 和 SUM 运算的数据量。
查询效率显著提升。
性能对比
在数据量较大时，比如有 1,000,000 条记录：

HAVING 方式：所有记录都会参与分组计算，资源消耗高，时间长。
WHERE 方式：只有满足 product_id = 101 的数据参与分组，减少了无用的计算。
优化后，查询时间可能降低 30%-50% 或更多，具体视数据量和索引情况而定。



SQL 查询执行顺序
SQL 的逻辑执行顺序与书写顺序不同，逻辑执行顺序如下：

FROM：从数据源（表、视图等）加载数据。
ON：应用 JOIN 条件（仅适用于多表查询）。
JOIN：将符合 ON 条件的表进行连接。
WHERE：过滤不符合条件的行（行级过滤）。
GROUP BY：将数据分组。
HAVING：对分组后的结果进行过滤（基于聚合条件）。
SELECT：选择所需的列或表达式。
DISTINCT：去重。
ORDER BY：对结果集排序。
LIMIT：返回指定数量的行。


执行顺序分析
优化后的 SQL 调整了条件位置：

WHERE 先执行：WHERE product_id = 101 在 GROUP BY 之前过滤掉了不符合条件的行，减少了参与分组的记录数量。
减少分组和聚合运算的负担：GROUP BY 和 SUM 只需处理过滤后的数据。
HAVING 用于聚合结果过滤：HAVING total_amount > 500 只对已经分组后的结果应用。
效果：大幅减少了分组和聚合运算的记录数，提升了性能。

区别：WHERE：在分组之前过滤数据，作用于行。
HAVING：在分组之后过滤数据，作用于聚合结果。