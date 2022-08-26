---
title: jconsole使用说明
description: jconsole使用说明
slug: jconsole使用说明
date: 2022-08-11 13:31:00+0000
categories:
    - Java
tags:
    - Jvm
---

# jconsole使用说明

JConsole 是一个内置 Java 性能分析器，可以从命令行（直接输入jconsole）或在 GUI shell （jdk\bin下打开）中运行。

它用于对JVM中内存，线程和类等的监控。可使用JTop插件。它可以监控本地的jvm，也可以监控远程的jvm，也可以同时监控几个jvm。 

这款工具的好处在于，占用系统资源少，而且结合Jstat，可以有效监控到java内存的变动情况，以及引起变动的原因。在项目追踪内存泄露问题时，很实用。

## 1 概览

以图标的形式显示堆内存使用量，线程使用量，类加载数量，以及CPU占用率

## 2 内存

这里比jvisualvm实时性要高一点，并且可以通过切换，来展示堆内存、非堆内存、PS Old Gen、PS Eden Space、PS Survivor Space、Metaspace、Code Cache、Compressed Class Space。等内存的使用图标。

## 3 线程

这里能显示活动线程数量以及峰值线程数量的变化图表。

下面的线程框，能查看每个线程的线程名称，以及线程的堆栈跟踪。还有一个按钮来支持死锁检测。

## 4 类

类页卡主要展示已加装当前类数量、已加载类总数、已卸载类总数等类相关信息

## 5 VM概要

vm概要主要显示堆信息、虚拟机信息、类路径等相关信息

## 6 MBean

JMX把所有被管理的资源都成为MBean（ManagedBean），这些MBean全部由MBeanServer管理，如果要访问MBean，可以通过MBeanServer对外提供的访问接口，例如RMI或HTTP。

### 6.1 spring boot程序接入Mbean

在启动程序添加注解@EnableMBeanExport，告知spring自动注册MBean

在Controller上，添加@ManagedResource注解 （objectName = "company:name = testName", descriptiopn = "描述信息"）

在值添加 @ManagedAttribute（descriptiopn = "描述信息"）

在方法上添加@ManagedOperation

@ManagedOperationParameter（name= "name", descriptiopn = "描述信息"）

## 7 注意事项

想监控JAVA程序需要在程序启动时，添加JMX相关参数。如果是本地连接，可以不加

