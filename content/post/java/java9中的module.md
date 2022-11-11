---
title: java9中的module
description: #
slug: java9中的module
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - JAVA
---

# java9中的module

1.模块的声明文件module-info.java位于src目录下，对整个模块进行描述。否则会提示

```
Module declaration should be located in a module's source root
```

2.当存在module-info.java文件时，使用idea对项目进行打包。module-info.java 也会参与编译到 jar包中

3.可以通过

```
jmod create --class-path xxx.jar xxx.jmod
```

将一个jar包文件打包成 jmod 模块。

如果尝试将一个不包含module-info.class 的jar包编译为jmod时。会提示 【错误: module-info.class not found】

4.可以通过

```
jmod list xxx.jmod
```

命令来查看jmod文件中包含的内容。

5.直接打包为jmod模块

```
javac -d mod/utils utils/module-info.java utils/org/example/utils/StringUtils.java
jmod create --class-path <module-info.java所在路径> xxx.jmod

javac -d mod/test test/module-info.java test/org/example/test/TestStringUtils.java --module-path mod/
jmod create --class-path <module-info.java所在路径> --main-class <main函数路径> xxx.jmod

// 运行module
java --module-path mod/ --add-modules utils,test org.example.test.TestStringUtils
```

需要使用命令，先对java文件进行编译，然后再对class文件进行打包。

6.使用jlink生成一个包含制定模块的JRE环境

```
jlink --module-path . --add-modules test,utils --output /jre
```

7.总结

jdk9提供了一个介于package和jar包中间的Module的概念。但是和jar包并不冲突。对于一个包含module-info.java的源码来说，可以打包为jar包或者jmod包。优势在于，可以通过jlink命令，对自己的module和依赖的必须module来构建一个运行时最小集合。减少文件和内存消耗大小。

一般来说使用模块和不使用模块对用户来说基本上是感觉不到的，因为你可以将模块的jar包当成普通的jar包来使用，也可以将普通的jar包当成模块的jar包来使用。

当使用普通的jar包时，JDK将会采用一种Automatic modules的策略将普通jar包当成module jar包来看待。

这里做了一个测试，当定义了一个module test1,但不导出任何东西。如果是一个普通非模块java程序 test2 使用，则可以正常访问到test1中定义的方法。当给

test2 添加了module-info.java 文件。则会提示调用的方法未导出。