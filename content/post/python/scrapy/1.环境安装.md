---
title: scrapy环境安装
description: scrapy环境安装
slug: scrapy环境安装
date: 2022-03-06 00:00:00+0000
categories:
    - Python
tags:
    - Python
---

Python在安装Scrapy框架时，使用常规的pip install Scrapy命令进行安装，失败。

搜索教程，发现是需要在安装scrapy之前预先安装几个环境wheel、lxml、Twisted、pywin32。

1.lxml ：lxml是python的一个解析库,支持HTML和XML的解析,支持XPath解析方式。

直接使用pip install lxml安装。安装前升级pip。python -m pip install --upgrade pip

2.Twisted：Python实现的基于事件驱动的网络引擎框架。

这个其实在安装Scrapy的时候会自动安装，但是他需要visual C++ Build Tools 2015。国内的网又下载不下来。所以需要手动安装。

安装whl的话需要先安装wheel，所以先pip install wheel。

然后去下载Twisted。

地址：http://www.lfd.uci.edu/~gohlke/pythonlibs/

下载对应python版本、系统版本的Twisted。

pip install 文件名安装。

3.pywin32 直接 pip install pywin32

4.scrapy 直接 pip install scrapy

总结来说：安装scrapy的问题，在于Twisted插件需要visual C++ Build Tools 2015。

而国内的墙阻止了访问请求，导致直接安装的失败。
