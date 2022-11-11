---
title: Linux安装go语言环境
description: Linux安装go语言环境
slug: Linux安装go语言环境
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---

### 安装GO语言环境

环境为ubuntu bionic。armhf架构。

#### 下载二进制文件

这里要注意，由于当前环境架构为armhf。所以需要下载ARMV6对应版本的GO语言环境。

下载地址 https://golang.google.cn/dl

#### 解压文件，移动目录到 /usr/local/go

#### 配置环境变量

export PATH=$PATH:/usr/local/go/bin
