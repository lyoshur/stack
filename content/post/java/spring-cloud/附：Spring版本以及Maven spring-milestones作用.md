---
title: 附：Spring版本以及Maven spring-milestones作用
description: 附：Spring版本以及Maven spring-milestones作用
slug: 附：Spring版本以及Maven spring-milestones作用
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - spring-cloud
---

本文非完全原创，仅仅是整理自网上博客加上一部分自己的理解。参考文章：  
* 徐小冠 https://blog.csdn.net/weixin_42114097/article/details/82354534
* Hermione Granger https://blog.csdn.net/qq_29951485/article/details/88723065

本文仅仅作为学习spring相关内容学习笔记使用。

### spring
spring的版本大致可以分为  
* PRE  
预览版(内部测试版):主要用作开发研究或测试。生产环境不可用。  
* SNAPSHOT  
快照版:表示不稳定，仍在继续改进版本。  
* Mx  
里程碑构建:项目的重要节点，可能不完整，仍然有问题。x为序号按顺序编号。
* RCx  
候选发布者:功能相对完整，应该非常稳定。存在的问题相对罕见和次要的，但值得报告，试图修复它们以便发布。x为序号按顺序编号。
* GA  
一般可用性（发布）; 应该非常稳定，功能齐全
* SR(RELEASE)  
服务版本(主要版本之后的后续维护版本-RELEASE)。

spring版本稳定顺序是递增的。在开发中应尽量选用相对稳定的版本。

### spring framework

截至当前2019年11月15日。官网显示最新版本为5.2.1[GA]

### spring boot

截至当前2019年11月15日。
官网显示最新版本为2.2.1[GA]

### spring cloud

截至当前2019年11月15日。
官网显示最新版本为Greenwich SR3[GA]  
spring cloud的版本并没有使用直接的数字形式，而是采用的伦敦地铁站的名字进行的命名。名称首字符按照A-Z进行排序，当进行了重大BUG修复或重点功能更新时，发布一个SRx版本，SR3即第三次。

### 关联关系
springboot可以理解为spring framework的一个封装升级。spring cloud则是依赖于spring boot构建的。  
由于这种特殊的依存关系，springboot在结合spring cloud使用时，需要额外注意版本间的依存关系，在不断的更新中，旧版本与新版本可能存在不兼容。

例如：  
Finchley 是基于 Spring Boot 2.0.x 构建的，不支持 Spring Boot 1.5.x  
Dalston 和 Edgware 是基于 Spring Boot 1.5.x 构建的，不支持 Spring Boot 2.0.x  
Camden 构建于 Spring Boot 1.4.x，但依然能支持 Spring Boot 1.5.x  

### spring-milestones
Spring Milestone repo是一个标准的Maven repo-plugin。  
Spring的政策是向那些有兴趣测试它们的公众发布里程碑版本。为了明确这些里程碑版本不会在生产代码中使用，它们将发布到单独的仓库而不是Maven Central。  
所以当使用非GA以及GA以上版本时，可以在POM文件中添加配置。
```xml
<repositories>
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/milestone</url>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```
snapshots false 不使用快照版本。
