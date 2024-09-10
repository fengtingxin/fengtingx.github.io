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
