---
title: Linux进入root失败
description: Linux进入root失败
slug: Linux进入root失败
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---

原文地址：https://jingyan.baidu.com/article/fd8044fa1e74035031137ae0.html

### 进入ROOT失败

ubuntu怎么切换到root用户，我们都知道使用su root命令，去切换到root权限，此时会提示输入密码，可是怎么也输不对，提示“Authentication failure”，

此时有两种情况一个是真的是密码错了，另一种就是刚安装好的Linux系统，没有给root设置密码。

#### 没有设置密码

可以通过sudo passwd root 设置root密码
