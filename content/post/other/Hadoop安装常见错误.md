---
title: Hadoop安装常见错误
description: Hadoop安装常见错误。
slug: Hadoop安装常见错误
date: 2022-03-06 00:00:00+0000
categories:
    - Java
tags:
    - Hadoop
---

#### 1.伪分布式节点启动报错

执行命令 ./start-dfs.sh

错误信息：
```log
Starting namenodes on [10.1.4.57]
ERROR: Attempting to operate on hdfs namenode as root
ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
ERROR: Attempting to operate on hdfs datanode as root
ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [10.1.4.57]
ERROR: Attempting to operate on hdfs secondarynamenode as root
ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
```

解决办法：

1.sbin/start-dfs.sh和sbin/stop-dfs.sh脚本头部指定
```sh
HDFS_DATANODE_USER=root
# HDFS_DATANODE_SECURE_USER=hdfs（此条功能暂时不明，注释后无影响）
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```
2.环境变量 hadoop-env.sh中指定用户名：（未验证）
```sh
export HDFS_DATANODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
```
注：***_USER设置错误，会报cannot set priority of datanode process 32156

#### 二、没有jps命令

1. Path路径没有指定jdk/bin目录

2. Jdk版本不正确，推荐oracle版本

#### 三、jps命令后，namenode节点没启动

在执行sbin/start-dfs.sh命令前，没有格式化namenode

#### 四、jps命令后，datanode节点没启动

常见于多次格式化namenode，造成namenode和datanode节点信息不一致，datanode无法正常启动。

原因是bin/hdfs namenode -format只会格式化namenode，并不会影响到datanode，如果再次格式化会导致datanode和namenode的clusterID不一致。解决方法：先停掉hadoop，把slaves的dfs/data的内容删除，再次启动后，会创建新的clusterID,也可以复制master的clusterID到slaves中。删除hadoop临时文件data和name文件夹，否则重新启动Hadoop时无法启动namenode。以为namenode格式化后ID会变，导致与集群ID不一致

#### 五、节点启动成功，web服务无法访问

1.Centos7中默认的防火墙为firewall与之前的版本使用iptables不一样。

关闭防火墙：systemctl stop firewalld.service

开启防火墙：systemctl start firewalld.service

关闭开机启动：systemctl disable firewalld.service

开启开机启动：systemctl enable firewalld.service

只关闭防火墙，不关闭开机启动仍无法访问。原因不明。

2.发现在Hadoop3.0中namenode的默认端口配置发生变化：从50070改为9870

#### 六、yarn启动报错

执行命令 sbin/start-yarn.sh

错误信息：
```log
Starting resourcemanager
ERROR: Attempting to operate on yarn resourcemanager as root
ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operatio                    n.
Starting nodemanagers
ERROR: Attempting to operate on yarn nodemanager as root
ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.
```
解决办法：
```log
YARN_RESOURCEMANAGER_USER=root
# HADOOP_SECURE_DN_USER=yarn
YARN_NODEMANAGER_USER=root
```

#### 七、yarn执行命令报错找不到或无法加载主类org.apache.hadoop.mapreduce.v2.app.MRAppMaster

解决办法：

1.在命令行下输入hadoop classpath命令，并将返回的地址复制

2.编辑yarn-site.xml，添加内容
```xml
<configuration>
    <property>
        <name>yarn.application.classpath</name>
        <value>输入刚才返回的Hadoop classpath路径</value>
    </property>
</configuration>
```
在所有的Master和Slave节点进行如上设置，设置完毕后重启Hadoop集群

#### 八、yarn的集群运行，出现 Current usage: 105.9 MB of 1 GB physical memory used; 2.2 GB of 2.1 GB virtual memory used. Killing container. 错误。

解决方法：在etc/hadoop/yarn-site.xml文件中，修改检查虚拟内存的属性为false
```xml
<property>
    <name>yarn.nodemanager.vmem-check-enabled</name>
    <value>false</value>
</property>
```
