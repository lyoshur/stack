---
title: python-orm数据库查询框架使用教程
description: sql数据库查询框架
slug: python-orm数据库查询框架使用教程
date: 2022-03-13 00:00:00+0000
categories:
    - Python
tags:
    - Python
---

## 3 进行简单数据查询

```python
# 获取Service 参数为数据库内的表名
test_service = factory.get_service("tb_test")
```

获取对应数据库表的service后，能够使用内置的常用增删改查方法，对数据库进行操作

### 3.1查询列表
```python
# 查询当前service的列表,其中参数为空字典，表示不使用任何搜索条件
list_data = test_service.get_list({})
print(list_data)

# 当需要分页时，可以使用
# start 开始查询的起点记录 0表示从第一条记录查询
# length 要查询几条数据
# order_by 列表排序条件 例如 id asc 正序 或 id desc 逆序
list_data = test_service.get_list({}, start=0, length=10, order_by="id")
print(list_data)

# 搜索name字段等于张三
# 当输入%时，表示要使用模糊搜索
# %张向前模糊，张%向后模糊，%张%全模糊
list_data = test_service.get_list({
    "name": "张三"
}, start=0, length=10, order_by="id")
print(list_data)
```

### 3.2 查询记录数量
```python
# 查询当前service的数据总数,其中参数为空字典，表示不使用任何搜索条件
count = test_service.get_count({})
print(count)

# 搜索name字段等于张三的记录数量
# 当输入%时，表示要使用模糊搜索
# %张向前模糊，张%向后模糊，%张%全模糊
count = test_service.get_count({"name": "张%"})
print(count)
```

### 3.3 查询单条记录
```python
# 搜索第一条记录，等同于get_list，但是只返回第一条记录
model = test_service.get_first({"name": "张三"})
print(model)

# 查询主键对应的记录，条件必须是主键且不能为空
model = test_service.get_model({"id": 1})
print(model)
```

### 3.4 判断记录是否存在

注意在liteorm==0.0.1或myorm==1.0.2中，这个方法存在BUG，请使用count。然后自行判断记录是否存在！

```python
# 判断记录是否存在，参数等同于get_list，但是只返回Boolean
exist = test_service.exist({})
print(exist)

# 判断记录是否存在，参数等同于get_list，但是只返回Boolean
exist = test_service.exist({
    "name": "张%"
})
print(exist)
```

### 3.5 新增记录
```python
# 插入记录 字典key对应数据库字段名
# 当主键设置了自增时，将值写为 0
test_service.insert({
    "id": 0,
    "name": "李四"
})
```

### 3.6 更新记录
```python
# 更新记录 字典key对应数据库字段名
# 根据主键更新，必须填写主键
test_service.update({
    "id": 1,
    "name": "李四+1"
})
```

### 3.7 删除记录
```python
# 根据主键删除记录，必须填写主键
test_service.delete({"id": 3})
```

## 4 自定义SQL

当Service中的方法，不满足需求时或需要自定SQL执行可以使用以下方法  
根据返回值不同，方法被分成三类

### 4.1 查询记录
```python
# 使用自定义SQL进行列表查询
# 需要参数化的变量 使用 #{变量名} 进行查询
data = factory.query("select * from tb_test where `name` = #{name}", {"name": "张三"})
print(data)
```

### 4.2 查询记录条数
```python
# 使用自定义SQL进行列表数量查询
# 需要参数化的变量 使用 #{变量名} 进行查询
number = factory.count("select count(1) from tb_test where `name` = #{name}", {"name": "张三"})
print(number)
```

### 4.3 执行查询
```python
# 使用自定义SQL进行数据插入
# 需要参数化的变量 使用 #{变量名} 进行查询
# 返回两个参数，第一个为插入记录的ID，第二个为本次操作影响的记录行数
id, _ = factory.exec("INSERT INTO tb_test(name) VALUES(#{name})", {"name": "张三"})
print(id)

# 使用自定义SQL进行数据插入
# 需要参数化的变量 使用 #{变量名} 进行查询
# 返回两个参数，第一个为插入记录的ID，第二个为本次操作影响的记录行数
_, row_number = factory.exec("UPDATE tb_test SET name = #{new_name} WHERE name = #{old_name}", {
    "new_name": "张三+1",
    "old_name": "张三"
})
print(row_number)
```

## 5 使用Mapper查询

### 5.1 使用Mapper独立文件查询

当有SQL需要复用，或希望将SQL放置到文件中统一管理时，可以使用Mapper文件进行  

Mapper文件内容完整结构如下  
mapper节点可以对数据查询结果中的字段重命名，类似 select name as record_name  
如果不需要也可以不写mapper节点  
sql为条sql节点，包含一个key，来作为这条sql的标识，value为sql具体内容  

示例：tb_test.mapper文件
```python
<xml>
    <mapper column="id" parameter="record_id"/>
    <mapper column="name" parameter="record_name"/>
    <sql>
        <key>GetList</key>
        <value>
            select * from tb_test where name = #{name}
        </value>
    </sql>
    <sql>
        <key>GetCount</key>
        <value>
            select Count(1) from tb_test where name = #{name}
        </value>
    </sql>
    <sql>
        <key>Insert</key>
        <value>
            INSERT INTO tb_test(name) VALUES(#{name})
        </value>
    </sql>
</xml>
```

然后将文件加载到框架中使用

```python
# 将mapper加载到框架中
# tb_test_dao 是随意起的一个名字
# 通过 orm.parse_config_from_file 将mapper文件加载到框架，用于生成dao实例
factory.load_dao("tb_test_dao", orm.parse_config_from_file("tb_test.mapper"))

# 使用加载后的DAO进行查询，这时的第一个参数，对应到mapper文件中sql节点里的Key关键字
data = factory.get_dao("tb_test_dao").query("GetList", {"name": "张三"})
print(data)
```

与自定义SQL查询相同，根据返回值不同，DAO实例提供了三个方法

```python
# 获取dao
tb_test_dao = factory.get_dao("tb_test_dao")

# 查询列表使用 dao.query
data = tb_test_dao.query("GetList", {"name": "张三"})
print(data)

# 查询记录数量使用 dao.count
count = tb_test_dao.count("GetCount", {"name": "张三"})
print(count)

# 执行SQL使用 返回值等同于 factory.exec
id, _ = tb_test_dao.exec("Insert", {"name": "张三"})
print(id)
```

### 5.2 使用Mapper格式字符串查询

框架也支持加载Mapper格式字符串

```python
# 定义一个Mapper格式的字符串
sql_xml = """
<xml>
    <mapper column="id" parameter="record_id"/>
    <mapper column="name" parameter="record_name"/>
    <sql>
        <key>GetList</key>
        <value>
            select * from tb_test where name = #{name}
        </value>
    </sql>
</xml>
"""

# 将mapper加载到框架中
# 通过 orm.parse_config_from_string 将mapper字符串加载到框架
factory.load_dao("tb_test_dao", orm.parse_config_from_string(sql_xml))

# 查询列表使用 dao.query
data = factory.get_dao("tb_test_dao").query("GetList", {"name": "张三"})
print(data)
```

## 6 事务支持

### 6.1 通过函数执行事务

```python
# 定义一个方法
# 将需要进行的数据库操作，放在方法内
def insert_all():
    factory.get_service("tb_test").insert({"id": 0, "name": "张三"})
    factory.get_service("tb_test").insert({"id": 0, "name": "李四"})


# 通过 factory.transaction 执行刚刚定义的方法
# 注意参数是方法本身 不要写成 factory.transaction(insert_all())
# 这样处于方法内部的查询，则被加入到同一个数据库事务中
factory.transaction(insert_all)
```

### 6.2 通过装饰器开启事务

```python
# 定义一个方法
# 将需要进行的数据库操作，放在方法内
@factory.transaction_wraps
def insert_all():
    factory.get_service("tb_test").insert({"id": 0, "name": "张三"})
    factory.get_service("tb_test").insert({"id": 0, "name": "李四"})


# 因为方法上被加上了 @factory.transaction_wraps 装饰器
# 则这个方法被执行时，会自动启用事务
insert_all()
```
