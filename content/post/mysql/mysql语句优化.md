---
title: MySQL语句优化
description: MySQL语句优化
slug: MySQL语句优化
date: 2022-03-06 00:00:00+0000
categories:
    - MySQL
tags:
    - MySQL
---

## mysql语句优化

1. 比较运算符能用 = 就不用 <>
2. 明知只有一条查询结果，那么请使用 LIMIT 1
3. 使用UNION ALL 代替 UNION
4. 尽量避免使用selece *
5. 尽量避免 IN 和 NOT IN
6. 尽量避免在where子句中使用 or 来连接条件
7. 使用 LIKE 尽量使用 XXX%，不要将%放在前面
8. 最小查询原则
9. 避免在索引列上使用mysql的内置函数
10. 避免在WHERE表达式上使用表达式操作
11. 避免在WHERE中使用 != 或 <>
12. 使用联合索引时，注意索引列的顺序，一般遵循最左匹配原则
13. 如果字段是字符串，WHERE子句一定要加引号