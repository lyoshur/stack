---
title: redis常见使用场景
description: redis常见使用场景
slug: redis常见使用场景
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - redis
---

## redis常见使用场景

### 1.缓存

对系统中的热点数据进行缓存，减轻数据库压力

### 2.跨应用的数据共享

通过多个应用连接到同一个redis实例，实现多个应用的数据共享。eg：分布式Session

### 3.分布式锁

通过共享redis中某个值，来判断能否获取到锁

```java
public static boolean getLock(String key) {
    Long flag = jedis.setnx(key, "1");
    if (flag == 1) {
        jedis.expire(key, 10);
    }
    return flag == 1;
}
public static void releaseLock(String key) {
    jedis.del(key);
}
```

### 4.全局ID

将一个全局的ID值，设置到redis。

int 类型 incrby 利用原子性

eg: 分库分表的常见，一次性拿一段 incrby userid 1000

### 5.计数器

文章的阅读量、点赞数之类的数据，写redis。再定时更新到数据库

### 6.限流

int类型 incr方法

以访问者的IP和其他信息做键。记录访问次数，超过访问次数则进行限制。

### 7.位统计

String 类型的 bitcount （1.6.6的 bitmap 数据结构介绍）

### 8.购物车

String 或 hash 。 所有String可以做的hash都可以做

### 9.用户消息时间线 timeline

list 双向链表，直接作为 timeline 就好了

### 10.消息队列

### 11.获取随机值

### 12.点赞、签到、打卡

sadd srem sismember scard

### 13.商品标签

### 14.商品筛选

### 15.用户关注、推荐模型

### 16.排行榜