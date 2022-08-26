---
title: 使用liteorm连接到sqlite3
description: sqlite3数据库查询框架
slug: 使用liteorm连接到sqlite3
date: 2022-03-11 00:00:00+0000
categories:
    - Python
tags:
    - Python
---

# liteorm sqlite3数据库查询框架

## 1 依赖安装

```python
pip install liteorm
```

## 2 连接数据库

```python
# 引入最好这样写，推荐写法
from liteorm import orm
# 连接数据库
factory = orm.Builder("test.db").build()
```
