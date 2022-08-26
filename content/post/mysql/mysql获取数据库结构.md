---
title: MySQL获取数据库结构
description: MySQL获取数据库结构
slug: MySQL获取数据库结构
date: 2022-03-06 00:00:00+0000
categories:
    - MySQL
tags:
    - MySQL
---

## mysql获取数据库结构

### 1.获取表信息

```mysql
SELECT
	TABLE_NAME, ENGINE, TABLE_COLLATION, TABLE_COMMENT, IFNULL(AUTO_INCREMENT, -1)
FROM
	information_schema.TABLES
WHERE
	TABLE_SCHEMA = #{ data_base_name } AND TABLE_TYPE = 'BASE TABLE'
```

| 字段名                     | 字段描述                         | 备注信息                                                     |
| -------------------------- | -------------------------------- | ------------------------------------------------------------ |
| TABLE_NAME                 | 表名                             |                                                              |
| ENGINE                     | 使用的查询引擎                   |                                                              |
| TABLE_COLLATION            | 表的排序规则                     |                                                              |
| TABLE_COMMENT              | 表的注释                         |                                                              |
| IFNULL(AUTO_INCREMENT, -1) | 如果有自动递增的话，当前的自增值 | 这里其实有点问题，在某些情况下，会导致数据库设置的是自增。但是找不到自增值 |

### 2.获取列信息

```mysql
SELECT
    ORDINAL_POSITION,
    COLUMN_NAME,
    COLUMN_TYPE,
    IS_NULLABLE,
    IFNULL(COLUMN_DEFAULT, ''),
    COLUMN_COMMENT
FROM
	information_schema.COLUMNS
WHERE
	TABLE_SCHEMA = #{ data_base_name } AND TABLE_NAME = #{ table_name }
```

| 字段名                     | 字段描述     | 备注信息      |
| -------------------------- | ------------ | ------------- |
| ORDINAL_POSITION           | 序号位置     | 从1开始的序号 |
| COLUMN_NAME                | 栏位名称     |               |
| TABLE_COLLATION            | 栏位数据类型 | varchar(32)   |
| IS_NULLABLE                | 是否允许为空 |               |
| IFNULL(COLUMN_DEFAULT, '') | 默认值       |               |
| COLUMN_COMMENT             | 栏位的描述   |               |

### 3.获取索引信息

```mysql
SELECT
    INDEX_NAME,
    COLUMN_NAME,
    NON_UNIQUE,
    INDEX_TYPE
FROM
	information_schema.STATISTICS
WHERE
	TABLE_SCHEMA = #{ data_base_name } AND TABLE_NAME = #{ table_name }
```

| 字段名      | 字段描述           | 备注信息      |
| ----------- | ------------------ | ------------- |
| INDEX_NAME  | 索引名称           | 主键是PRIMARY |
| COLUMN_NAME | 索引对应的栏位信息 |               |
| NON_UNIQUE  | 是否唯一索引       |               |
| INDEX_TYPE  | 索引类型           | BTREE         |