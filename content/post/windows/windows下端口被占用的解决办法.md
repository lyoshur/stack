---
title: windows关于端口被占用的解决办法
description: windows关于端口被占用的解决办法
slug: windows关于端口被占用的解决办法
date: 2022-03-06 00:00:00+0000
categories:
    - Windows
tags:
    - Windows
---

#### windows下端口被占用的解决办法

使用命令： 
netstat -aon|findstr "8080"

这里的8080为模拟的端口号。记住返回结果集的PID值。

使用命令： 
taskkill -F /pid "2323"

这里的2323为模拟的PID。执行命令杀死进程。
