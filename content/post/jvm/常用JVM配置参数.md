---
title: 常用JVM配置参数
description: 常用JVM配置参数
slug: 常用JVM配置参数
date: 2022-08-26 16:31:00+0000
categories:
    - Java
tags:
    - Jvm
---

# 常用JVM配置参数

## -Xms

初始堆内存大小，默认为物理内存的1/64（等价于-XX:InitialHeapSize）

## -Xmx

最大堆内存大小，默认为物理内存的1/4（等价于-XX:MaxHeapSize）

## -Xmn

设置年轻代大小（等价于-XX:MaxNewSize=512m）

## -Xss

设置单个线程的线程栈大小，一般默认512k到1024k（等价于-XX:ThreadStackSize）

## -XX:MetaspaceSize

设置元空间大小（**元空间与永久代最大的区别为：元空间并不在虚拟机中，而使用的是本地内存，因此，元空间只收本地内存的限制**）

## -XX:MaxMetaspaceSize

设置最大元空间大小

## -XX:+PrintGCDetails

输出详细GC收集日志信息

## -XX:SurvivorRatio

设置新生代中 eden 和 S0/S1 空间比例，默认 -XX:SurvivorRatio=8，Eden : S0 : S1 = 8 : 1 : 1

## -XX:NewRatio

配置年轻代和老年代在堆结构的占比，默认 -XX:NewRatio=2 新生代占1，老年代占2，年轻代占整个堆的 1/3

## -XX:MaxTenuringThreshold

设置垃圾最大年龄。**默认是15**。-XX:MaxTenuringThreshold=0：设置垃圾最大年龄。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入老年代。对于老年代比较多的应用，可以提高效率。如果此值设置为一个较大的值，则年前对象会在Survivor区进行多次复制，这样可以增加对象在年轻代的存活时间，增加在年轻代被回收的概率！

## -XX:MinHeapFreeRatio=40

堆内存使用率小于40时缩减堆内存，xms=xmx时该参数无效，默认值40

## -XX:MaxHeapFreeRatio=70

堆内存使用率大于70时扩张堆内存，xms=xmx时该参数无效，默认值70

## -XX:+UseSerialGC

开启 Serial垃圾收集器（新生代）
新生代使用Serial  老年代则使用SerialOld

## -XX:-UseSerialGC

关闭 Serial垃圾收集器（新生代）

## -XX:+UseParNewGC

开启ParNew垃圾收集器（新生代）
新生代使用功能ParNew 老年代则使用功能CMS

## -XX:-UseParNewGC

关闭ParNew垃圾收集器（新生代）

## -XX:+UseParallelOldGC

开启Parallel Scavenge收集器（新生代）

## -XX:-UseParallelOldGC

关闭Parallel Scavenge收集器（新生代）

## -XX:+UseParallelGC

开启ParallelOl垃圾收集器（老年代）

## -XX:-UseParallelGC

关闭ParallelOl垃圾收集器（老年代）

## -XX:+UseConcMarkSweepGC

开启CMS垃圾收集器（老年代）

## -XX:-UseConcMarkSweepGC

关闭CMS垃圾收集器（老年代）

## -XX:+UseG1GC

开启G1垃圾收集器

## -XX:-UseG1GC

关闭G1垃圾收集器

## -XX:ParallelGCThreads=16

GC并行执行线程数

## -XX:PretenureSizeThreshold=1000000

新生代可容纳的最大对象，大于此值的对象直接会分配到老年代，设置为0则没有限制。
避免在Eden区和Survivor区发生大量的内存复制，该参数只对Serial和ParNew收集器有效，Parallel Scavenge并不认识该参数

## -XX:InitialTenuringThreshol=7

进入老年代最小的GC年龄

## -XX:MaxTenuringThreshold=15

进入老年代最大的GC年龄

## -Xloggc:/home/GCEASY/gc-%t.log

配置GC文件路径

## -XX:+UseGCLogFileRotation

开启滚动生成日志

## -XX:NumberOfGCLogFiles=4

滚动GC日志文件数，默认0，不滚动

## -XX:GCLogFileSize=100k

GC文件滚动大小，需配置UseGCLogFileRotation，设置为0表示仅通过jcmd命令触发

## -XX:+PrintGCApplicationStoppedTime

打印应用暂停时间

## -XX:+PrintHeapAtGC

每次GC完成后，打印出JVM堆内存每个区域的使用情况

## -XX:+PrintTenuringDistribution

打印存活实例年龄信息

## -XX:+HeapDumpOnOutOfMemoryError

抛出内存溢出错误时导出堆信息到指定文件

## -XX:HeapDumpPath=/data/dump/jvm.dump

设置存溢出错误时导出堆信息到指定文件，文件路径

## -XX:+HeapDumpBeforeFullGC

实现在Full GC前dump

## -XX:+HeapDumpAfterFullGC

实现在Full GC后dump

## -XX:HeapDumpPath=e:\dump

设置Dump保存的路径