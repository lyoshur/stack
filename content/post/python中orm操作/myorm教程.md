---
title: 使用myorm连接到mysql
description: mysql数据库查询框架
slug: 使用myorm连接到mysql
date: 2022-03-12 00:00:00+0000
categories:
    - Python
tags:
    - Python
---

# myorm mysql数据库查询框架

## 1 依赖安装

```python
pip install mysqlconn
```

## 2 连接数据库

```python
# 引入最好这样写，推荐写法
from myorm import orm

# 连接数据库
factory = orm.Builder(
    "127.0.0.1",
    "root",
    "123456",
    "test",
    3306
).build()

# 从数据库解析数据库表结构，加载通用的Service
# 全局仅需执行一次！！
# 建议放到数据库连接之后直接执行
factory.load_service()
```
