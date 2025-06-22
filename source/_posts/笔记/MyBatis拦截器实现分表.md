---
title: MyBatis拦截器实现分表 
date: 2025-02-22 17:33:00
tags:
	- MyBatis
categories: 分表
---

# 👌MyBatis拦截器实现分表


# <font style="color:rgb(47, 48, 52);">背景</font>
<font style="color:rgb(47, 48, 52);">所负责的业务中有一个 xx 系统，其中的 xx 核心表增长速度非常快，每日增长 200W 的数据量，业务特性主要是查询最近一周的数据，那么随着数据量越来越多，查询性能疯狂下降。这是典型的冷热数据场景，于是要开始进行冷热分离的操作。</font>

<font style="color:rgb(47, 48, 52);">常见的两种方案，一种是删除/归档旧数据，一种是数据分表。</font>

# <font style="color:rgb(47, 48, 52);">  
</font><font style="color:rgb(47, 48, 52);">方案选择</font>
## <font style="color:rgb(47, 48, 52);">归档/删除旧数据</font>
<font style="color:rgb(47, 48, 52);">这种方式最常见的发难就是将冷数据移动到归档表或者直接进行删除，从而减少表的大小。</font>

<font style="color:rgb(47, 48, 52);">实现思路非常简单，一般写一个定时任务不断的跑就可以了。</font>

<font style="color:rgb(47, 48, 52);">缺点：</font>

<font style="color:rgb(47, 48, 52);">1、数据删除会影响数据库性能，引发慢sql，多张表并行删除，数据库压力会更大。</font>

<font style="color:rgb(47, 48, 52);">2、频繁删除数据，会产生数据库碎片，影响数据库性能，引发慢SQL。</font>

<font style="color:rgb(47, 48, 52);">有一定的风险，选择另一种分表的方案。</font>

## <font style="color:rgb(47, 48, 52);">分表</font>
<font style="color:rgb(47, 48, 52);">业务特性是查最近的一周的数据，按日期的水平拆分是合理的。每周生成一张新表，同时此表只放本周的数据，这样表内数据量得到了控制，不会很大。</font>

### <font style="color:rgb(47, 48, 52);">分表方案选型</font>
<font style="color:rgb(47, 48, 52);">分别常见的主要考虑2种分表方案：Sharding-JDBC、利用Mybatis自带的拦截器特性。</font>

<font style="color:rgb(47, 48, 52);">经过对比后，决定采用Mybatis拦截器来实现分表。</font>

<font style="color:rgb(47, 48, 52);">1、JAVA生态中很常用的分表框架是Sharding-JDBC，虽然功能强大，但需要一定的接入成本，并且很多功能暂时用不上。</font>

<font style="color:rgb(47, 48, 52);">2、系统本身已经在使用Mybatis了，只需要添加一个mybaits拦截器，把SQL表名替换为新的周期表就可以了，没有接入新框架的成本，开发成本也不高。</font>

### <font style="color:rgb(47, 48, 52);">分表具体实现思路</font>
1、通过拦截器获取表名

<font style="color:rgb(51, 51, 51);">String tableName = TemplateMatchService.matchTableName(boundSql.getSql().trim());</font>

2、根据数据日期进行计算后缀

3、替换原有 sql，最终执行。

<font style="color:rgb(51, 51, 51);"> String rebuildSql = boundSql.getSql().replace(shardingProperty.getTableName(), shardingTable);</font>

<font style="color:rgb(51, 51, 51);"> metaStatementHandler.setValue(ORIGIN_BOUND_SQL, rebuildSql);</font>

<font style="color:rgb(47, 48, 52);">  
</font>

<font style="color:rgb(0, 0, 0);">  
</font>