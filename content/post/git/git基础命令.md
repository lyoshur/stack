---
title: git基础命令
description: #
slug: git基础命令
date: 2022-03-06 00:00:00+0000
categories:
    - GIT
tags:
    - #
---

## git基础命令

### 1.创建一个空仓库

```bash
git init
```

### 2.获取配置信息

```bash
git config --list
```

### 3.配置个人的用户名和电子邮件地址

```bash
git config --global user.name "lyoshur"
git config --global user.email "1421333878@qq.com"
```

如果去掉 --global 则只对当前仓库有效

### 4.将文件加入到版本控制（添加文件到暂存区）

```bash
git add *.java
git add README
```

### 5.将文件提交到仓库（提交暂存区到本地仓库）

```bash
git commit -m "提交的描述信息"
```

可以使用 -a 来提交暂存区的全部内容

### 6.从现有仓库中拷贝项目

```bash
git clone <repo>
```

### 7.获取当前工作区状态

```bash
git status
```

可以使用 -s 参数，来获取简短的输出结果

### 8.比较文件在暂存区和工作区的区别

```bash
git diff [file]
git diff --cached
git diff --staged
git diff HEAD
```

### 9.回退版本

```bash
git reset --soft HEAD
```

### 10.查看历史记录

```bash
git log
```

可以使用 --oneline 来查看历史记录的简洁的版本

可以使用 --graph 来查看历史记录什么时候出现了分支、合并等情况

### 11.查看指定文件的修改情况

```bash
git blame <file>
```

