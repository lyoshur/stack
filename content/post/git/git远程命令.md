---
title: git远程命令
description: git远程命令
slug: git远程命令
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - GIT
---

## git远程命令

### 1.显示所有远程仓库

```bash
git remote -v
```

### 2.显示某个远程仓库的信息

```bash
git remote show [remote]
```

### 3.添加远程仓库

```bash
git remote add [shortname] [url]
git remote add origin git@github.com:xxxxxxxxx
```

### 4.推送到远程仓库

```bash
git push -u origin master
```

### 5.将服务器上的最新代码拉取到本地

```bash
git pull origin master
```

### 6.删除远程仓库

```bash
git remote rm [name]
```

### 7.修改仓库名

```bash
git remote rename [old_name] [new_name]
```

