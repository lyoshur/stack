---
title: 关于maven中的Group、Artifact、Version
description: #
slug: 关于maven中的Group、Artifact、Version
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - maven
---

本文非完全原创，仅仅是整理自网上博客加上一部分自己的理解。参考文章：  
* aigoV https://blog.csdn.net/aigoV/article/details/89884501

本文仅仅作为学习maven相关内容学习笔记使用。

### Maven
1.GroupId(Group):  
项目所属组织的唯一标识符，对应项目中java的包结构（main目录里java的目录结构）。一般是公司官网域名反写或组织名：比如com.baidu.项目名或com.aigov.项目名。  
GroupID定义了项目属于哪一个组。

2.ArtifactId(Artifact):  
项目的唯一标识符，对应项目的名字，是项目根目录的名称。  
ArtifactID定义了一个maven项目在组中的唯一id。

3.Version  
指定项目当前的版本，SNAPSHOT意为快照，说明该项目还处于开发中，是不稳定的版本。

#### 整理

在maven中，所有的依赖被处理成由Group、Artifact、Version三个标识所确定的坐标中。

因此在个人项目中，应尽量避免使用与已存在的maven项目相同的标识。除非他们处于同一个组织或项目。  
另外一般包名与Group、Artifact会存在对应关系，所以起名时应注意包名起名规范。  
