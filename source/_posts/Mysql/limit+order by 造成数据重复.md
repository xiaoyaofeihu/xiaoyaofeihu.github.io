---
title: limit+order by 造成的数据重复
date: 2025-08-10 13:33:41
categories: mysql
---

### 问题解释与分析

在使用 SQL 查询进行分页时，`LIMIT` 和 `ORDER BY` 是两个常用的子句。`ORDER BY` 用于指定排序的列，而 `LIMIT` 用于限制返回的记录数。然而，当 `ORDER BY` 的列（如 `create_time`）中存在重复值时，就可能出现分页问题。

#### 原因分析

1. **排序不唯一**：当 `create_time` 有重复值时，数据库在返回结果时对于这些具有相同 `create_time` 的记录没有固定的排序顺序。这意味着，即使你多次执行相同的查询，具有相同 `create_time` 的记录的顺序也可能不同。

2. **分页边界问题**：假设你在分页时，每页显示固定数量的记录。如果两页之间的边界处有多条记录具有相同的 `create_time` 值，这些记录可能会在不同的页中出现，或者在两次查询中的顺序不同，导致重复出现或遗漏。

#### 示例场景

假设有以下数据：

```
id | create_time
1  | 2023-01-01
2  | 2023-01-01
3  | 2023-01-02
4  | 2023-01-02
```

如果每页显示2条记录，可能出现以下情况：

- 第一次查询可能返回 ID 1 和 2。
- 第二次查询可能返回 ID 2 和 3（ID 2 重复出现）。
- 或者第一次返回 ID 1 和 3，第二次返回 ID 2 和 4（数据遗漏）。

#### 解决方案

为了避免这种问题，可以采取以下几种解决方案：

1. **使用唯一键作为次要排序条件**：
   在 `ORDER BY` 子句中添加一个唯一键（如 `id`）作为次要排序条件。这样可以确保排序结果是确定性的，即使 `create_time` 有重复值。

   ```sql
   SELECT * FROM table ORDER BY create_time, id LIMIT 2 OFFSET 0; -- 第一页
   SELECT * FROM table ORDER BY create_time, id LIMIT 2 OFFSET 2; -- 第二页
   ```

2. **使用游标分页**：
   基于上一页最后一条记录的值来确定下一页的开始位置。这种方法可以避免因为排序不唯一而导致的问题。

   ```sql
   -- 第一页
   SELECT * FROM table ORDER BY create_time, id LIMIT 10;

   -- 后续页（假设上一页最后一条记录的 create_time 和 id 分别为 last_seen_time 和 last_seen_id）
   SELECT * FROM table 
   WHERE (create_time > 'last_seen_time') 
      OR (create_time = 'last_seen_time' AND id > last_seen_id)
   ORDER BY create_time, id 
   LIMIT 10;
   ```

3. **使用窗口函数**（某些数据库支持）：
   使用窗口函数如 `ROW_NUMBER()` 来为每行分配一个唯一的行号，然后根据行号进行分页。

   ```sql
   SELECT * FROM (
     SELECT *, ROW_NUMBER() OVER (ORDER BY create_time, id) as row_num
     FROM table
   ) t WHERE row_num BETWEEN 11 AND 20; -- 假设要查询第2页，每页10条记录
   ```

#### 结论

当 `ORDER BY` 的列有重复值时，单纯使用 `LIMIT` 分页确实可能导致数据重复或遗漏。为了确保分页结果的一致性，最佳实践是添加一个唯一列作为次要排序条件，或者使用游标分页、窗口函数等更复杂的分页方法。这些方法可以确保即使在 `ORDER BY` 的列有重复值的情况下，分页结果也是确定和一致的。