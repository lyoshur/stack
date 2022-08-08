---
title: java反编译
description: java反编译
slug: java反编译
date: 2022-03-08 00:00:00+0000
categories:
    - Java
tags:
    - Java
---

## java反编译

在使用idea进行反编译class文件时，发现文件头会带有

```bash
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//
```

于是想利用idea自带的反编译插件，来对已经存在的JAR包进行反编译。

不过在idea安装目录，插件中查找没有找到Fernflower。后搜索教程，教程说内置的插件名称是java-decompiler

这里怀疑是idea对Fernflower进行了插件化封装。

使用反编译命令，对jar包进行反编译

```bash
java -cp java-decompiler.jar org.jetbrains.java.decompiler.main.decompiler.ConsoleDecompiler -hdc=0 -dgs=1 -rsy=1 -rbr=1 -lit=1 -nls=1 -mpm=60 <jar> <反编译后的JAR生成目录>
```

-cp <目录和 zip/jar 文件的类搜索路径>

反编译后生成结果仍然是JAR包，但是可以使用命令

```bash
jar -xf <jar>
```

对反编译生成的JAR包进行解包，解包结果就是反编译后的class文件

！使用命令对 java-decompiler.jar 进行解包

```xml
<id>org.jetbrains.java.decompiler</id>
<name>Java Bytecode Decompiler</name>
<description>
    Extends standard .class file viewer with the Fernflower Java decompiler
</description>
<vendor>JetBrains</vendor>
```

确实，这个插件是继承自 Fernflower 的