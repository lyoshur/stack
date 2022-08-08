---
title: git分支管理
description: git分支管理
slug: git分支管理
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - GIT
---

## git分支管理

### 1.查看全部分支

```bash
git branch
```

### 2.创建分支命令

```bash
git branch [branchname]
```

如果觉得之前的分支名不合适，可以为新建的分支重命名 git branch -m [branchname] [branchname]

### 3.切换分支命令

```bash
git checkout [branchname]
```

### 4.合并分支

```bash
git merge
```

### 5.删除分支

```bash
git branch -d [branchname]
```

### 6.查看全部标签

```bash
git tag
```

### 7.创建标签

```bash
git tag -a v1.0.0
```

### 8.为某个提交打标签

```bash
git tag -a v0.9.9 [85c7e7]
```

### 9.为某个标签添加描述信息

```bash
git tag -a [tagname] -m [描述信息]
```

