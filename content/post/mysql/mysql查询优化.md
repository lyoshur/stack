---
title: MySQL查询优化
description: #
slug: MySQL查询优化
date: 2022-03-06 00:00:00+0000
categories:
    - MySQL
tags:
    - MySQL
---

## mysql查询优化

1. 为列选择合适的数据类型
2. 将大的DELETE \ UPDATE \ INSERT查询变成多个小查询
3. 为获得相同结果集的多次执行，请保持SQL语句的前后一致
4. WHERE 子句中的列尽量被索引
5. JOIN 子句中的列尽量被索引
6. ORDER BY 的列尽量被索引
7. 使用 EXPLAIN 关键字去查看执行计划