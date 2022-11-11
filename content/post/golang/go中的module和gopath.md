---
title: go中的module和GOPATH
description: go中的module和GOPATH
slug: go中的module和GOPATH
date: 2022-03-06 00:00:00+0000
categories:
    - Golang
tags:
    - #
---

# go中的module和GOPATH

在GO 1.11 中，开始使用module组织项目依赖。

首先是差别，GOPATH要求项目按照工程名-src。全部的依赖会放置到 GOPATH/src中。这里类似于java最开始lib的方式。

新的module方式，会在项目下生成一个go.mod文件，来对当前项目的模块名和依赖进行描述。

1.需要注意，使用命令go mod init <module_name> 对模块进行初始化时，虽然没有要求，但是在被其他模块 go get 时，仍要求模块名等同于路径名。

eg :  gitee.com/lyoshur/golog

2.在定义git标签时，应该尽量避免使用已经存在的标签，否则可能因为缓存问题，导致依赖下载出问题。

3.一个常用的GOPROXY

```
https://goproxy.cn,direct
```

