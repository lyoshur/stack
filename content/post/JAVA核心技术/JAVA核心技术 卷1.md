---
title: JAVA核心技术 卷1学习笔记
description: JAVA核心技术 卷1学习笔记
slug: JAVA核心技术 卷1学习笔记
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - Java
---

## JAVA核心技术 卷1

jdk9 jshell

jdk10 var a = 12;

printf 格式化 58页

基本类型

| 类型   | 存储需求 | 取值范围                                   |
| ------ | -------- | ------------------------------------------ |
| int    | 4字节    | -2147483648~2147483647（刚刚超过20亿）     |
| short  | 2字节    | -32768~32767                               |
| long   | 8字节    | -9223372036854775808~9223372036854775807   |
| byte   | 1字节    | -128~127                                   |
| float  | 4字节    | +-3.40282347E+38F（有效位数为6~7位）       |
| double | 8字节    | +-1.79769313486231570E+308(有效位数为15位) |
| char   |          |                                            |

1. 长整型以后缀 L 或 l 结尾
2. 十六进制使用前缀 0x
3. 八进制使用前缀 0
4. 二进制使用前缀 0b 或 0B
5. 数字字面量可以加下划线如  1_000_000
6. float类型数值使用后缀 F 或 f
7. double类型使用后缀 D 或 d
8. 还有三个特殊值  正无穷、负无穷、非数字  Double.POSITIVE_INFINITY Double.NEGATIVE_INFINITY 和 Double.NaN

大数 BigInteger BigDecimal

这本书好像是提供给C++程序员转java用的 = =

javadoc

Integer 和 int 包装类区别

List<Integer> 和 int[]



