---
title: Linux修改环境变量
description: Linux修改环境变量
slug: Linux修改环境变量
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---

### 修改环境变量

环境为ubuntu bionic。armhf架构。

#### 修改 /etc/profile 文件

在文件末尾添加  
export PATH=$PATH:/usr/local/go/bin

刷新环境变量  
source /etc/profile
