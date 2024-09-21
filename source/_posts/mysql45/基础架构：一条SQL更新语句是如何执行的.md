---
title: 基础架构：一条SQL更新语句是如何执行的
date: 2024-07-31 23:27:35
categories:
  - Mysql
tags:
  - MqSQL实战45讲
img: /2024/07/31/mysql45/ji-chu-jia-gou-yi-tiao-sql-geng-xin-yu-ju-shi-ru-he-zhi-xing-de/cover.jpg
cover: true
summary: 主要包含binlog、redolog内容
---

![1](1.png)

还是看这个图，举例说明：

```sql
mysql> create table T(ID int primary key, c int);

-- 当在id = 2这一行操作update时
mysql> update T set c=c+1 where ID=2;
```

具体的执行过程涉及到两个日志：redolog（重做日志）和 binlog（归档日志）

redolog的写入包含了两个步骤：prepare和commit，这就是两阶段提交.



## redolog

Mysql最主要的就是先写日志，再写磁盘.

简介：Innodb中的日志模块，存储的是物理地址，且redolog日志的大小是固定的。日志从头开始写，写到末尾又到开头循环写。

![2](20240912233620944.png)

> 绿色部分为可写入部分

write_pos 是当前写的位置

checkpoint 是当前要擦除的位置，也是往后推移并且循环的，擦除记录前要把记录更新到数据文件。

## binlog

两个日志的区别：

- redolog是InnoDB引擎特有的，binlog是Server层的日志.
- redolog是物理日志，记录的是“在数据页上做了什么修改”；binlog是逻辑日志记录的是这个语句的原始逻辑.binlog有两种格式：一种记录的是sql语句，一种记录是改变前的数据和改变后的数据.
- redolog是循环写的，空间固定；binlog是追加写的，binlog文件写到一定大小之后会切换到下一个，并不会覆盖之前的日志.



场景举例：

- Binlog如果已经写入，那么redolog是prepare, binlog已经完整了，这时候崩溃恢复过程会认可这个事务，提交。 
