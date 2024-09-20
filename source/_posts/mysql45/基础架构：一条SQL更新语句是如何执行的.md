---
title: 基础架构：一条SQL更新语句是如何执行的
date: 2024-07-31 23:27:35
categories:
  - Mysql
tags:
  - MqSQL实战45讲

---

![1](1.png)

还是看这个图，举例说明：

```mysql
mysql> create table T(ID int primary key, c int);

-- 当在id = 2这一行操作update时
mysql> update T set c=c+1 where ID=2;

```



具体的执行过程涉及到两个日志：redolog（重做日志）和 binlog（归档日志）

## redolog

Mysql最主要的就是先写日志，再写磁盘.

简介：Innodb中的日志模块，存储的是物理地址，且redolog日志的大小是固定的。日志从头开始写，写到末尾又到开头循环写。

![2](20240912233620944.png)

> 绿色部分为可写入部分

write_pos 是当前写的位置

checkpoint 是当前要擦除的位置，也是往后推移并且循环的，擦除记录前要把记录更新到数据文件。

## binlog

binlog是Server层的日志.



