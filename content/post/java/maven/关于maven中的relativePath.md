---
title: 关于maven中的relativePath
description: #
slug: 关于maven中的relativePath
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - maven
---

本文非完全原创，仅仅是整理自网上博客加上一部分自己的理解。参考文章：  
* xinwendewen https://blog.csdn.net/zwt0909/article/details/52218222

本文仅仅作为学习maven相关内容学习笔记使用。

### parent.relativePath

一般出现在pom文件，存在继承关系中。例如：
```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

这里的relativePath表示依赖路径。默认值为../pom.xml。  

也就是说，默认是从上层项目中获取父依赖，当前项目为依赖的子项目。

查找循序一般为relativePath地址–本地仓库–远程仓库。

也就是将查找父项目。父项目不存在的话，查找本地仓库。本地仓库仍然不存在，则查找远程仓库，直至查找失败。

而org.springframework.boot作为一个不存在本地的父项目。可以将relativePath的值设置为空。也就是<relativePath/>。这样将始终从仓库中获取，不存本地路径中获取。
