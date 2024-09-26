---
title: MySQL数据类型
date: 2022-11-14 00:04:43
tags:
  - MYSQL
---

**选择优化的数据类型**

- 更小的通常更好

  一般情况下，应该尽量使用可以正确存储数据的最小数据类型。

- 简单更好

  例如：应该用MySQL内建的类型（date、datetime、time）而不是字符串来存储日期和时间；应该用整型存储IP地址。

- 尽量避免NULL

  通常情况下最好指定列为NOT NULL，除非真的要存储NULL值。

  但是：如果计划在列上建立索引，就应该尽量避免设计为可为NULL的列。

  当然也有例外：InnoDB使用单独的位（bit）存储NULL值，对于稀疏数据（很多值为NULL只有少量行的列为非NULL）有很好的效率。

datetime 和timestamp可以存储相同类型的数据，然而timestamp存储空间是datetime的一半，并且有自动更新的能力；但timestamp允许的时间范围要小得多。

###### 整数类型

tinyint、smallint、mediumint、int、bigint。分别使用8、16、32、64、位存储空间，值的范围从-2^(N-1)^ 到2^(N-1)^-1

