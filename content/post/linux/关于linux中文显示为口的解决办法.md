---
title: 关于linux中文显示为口的解决办法
description: #
slug: 关于linux中文显示为口的解决办法
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---

原文地址：https://www.cnblogs.com/qinlangsky/p/11445296.html

### 关于linux中文显示为口的解决办法

环境为ubuntu bionic。armhf架构。

新安装的环境在chromium中无法显示中文。经检查是缺少中文字体库导致的。

#### 解决步骤

1.从windows复制或主动下载中文字体库

windows字体库路径：C:\Windows\Fonts

这里我自己选择的是宋体simsun.ttc这个文件

2.在linux字体目录创建一个文件夹用于存放添加的字体库(Linux默认的字体目录：/usr/share/fonts)

cd /usr/share/fonts  
mkdir fontpackages

3.复制字体库到新建的字体库目录

cp simsun.tcc /usr/share/fonts/fontpackages

4.加载字体库中的字体

fc-cache -fv
