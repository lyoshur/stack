---
title: java日志基本知识
description: java日志基本知识
slug: java日志基本知识
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - Java
---

## java日志基本知识

### 1.为什么要使用日志

- 可随时开闭日志记录，分级别筛选日志，并且保留日志代码开销很小

- 日志可以简单地被定向到控制台显示、文件保存、网络传输

- 日志可格式化其记录的格式

- 日志可由配置文件控制

- 日志利于日后错误的定位

### 2.日志的发展史

- Apache的 log4j 日志框架最早出现（可用配置文件管理日志并动态加载）

- java1.4 后面才添加的标准日志库 java.util.logging

- Apache推出日志门面Apache Commons Logging (JCL 提供了一套日志接口，兼容上面两者)

- JCL的作者弄了个新的日志门面 SLF4J，并提供了其组件实现 logback

- Apache重写log4j，推出log4j2

- 因为slf4j门面后面才出现，所以推出了各种补丁使其兼容JCL的接口

使用日志框架需要选择一个日志门面，然后再选择个门面的实现，不选择实现的话默认使用java的标准库

### 3.java标准日志库

#### 3.1关键概念

- 日志记录器（Logger） 面向操作的Api 提供了常用的 info warning 等接口

- 日志管理器（Manager） 加载配置文件

- 日志处理器（Handler）将日志输出到控制台或文件

- 日志过滤器 （Filter） 过滤器定义规则对日志内容进行过滤

- 日志格式化器（Format） 用来格式化日志记录

#### 3.2示例代码

```java
package com.company;

import java.io.IOException;
import java.util.logging.*;

public class Main {

    public static void main(String[] args) throws IOException {

        Formatter formatter = new Formatter() {
            @Override
            public String format(LogRecord record) {
                return "hahahah" + record.getMessage() + "-";
            }
        };

        // 获取到一个全局的日志记录器，也可以指定参数获取一个关联到包的记录器
        Logger logger = Logger.getGlobal();
        // 准备为记录器添加一个处理器
        ConsoleHandler consoleHandler = new ConsoleHandler();
        // 为处理器添加格式化器
        consoleHandler.setFormatter(formatter);
        // 设置不触发父记录器，否则日志会被打印两份
        logger.setUseParentHandlers(false);
        // 为记录器添加一个处理器
        logger.addHandler(consoleHandler);
        // 输出日志
        logger.info("ASD");
    }
}

```

