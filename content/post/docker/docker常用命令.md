---
title: docker常用命令
description: docker常用命令
slug: docker常用命令
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - Docker
---

## docker常用命令

### 1.获取本地镜像列表

```bash
docker images
```

### 2.拉取镜像

```bash
docker pull ubuntu:15.10
```

### 3.容器相关操作

#### 3.1使用镜像启动一个新的容器

```bash
docker run -i -t ubuntu:15.10 /bin/bash
```

-i 交互式操作

-t 终端

-d 后台启动

-p 指定端口映射

--name 指定容器名

--network 指定要连接的Docker网络

只指定 -i 会导致能进行命令交互，但没有 [ root@lyoshur-PC:/home/lyoshur#  ] 信息

只指定 -t 貌似命令执行有些问题

#### 3.2在后台启动容器

```bash
docker run -d ubuntu:15.10 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

-d 在后台启动容器

#### 3.3启动一个已停止的容器

```bash
docker start <容器ID>
```

#### 3.4停止容器

```bash
docker stop <容器ID>
```

#### 3.5查看全部容器

```bash
docker ps -a
```

#### 3.6使用attach进入容器

```bash
docker attach <容器ID>
```

#### 3.7使用exec进入容器

```
docker exec -it <容器ID> /bin/bash
```

#### 3.8导出容器

```bash
docker export <容器ID> > ubuntu.tar
```

#### 3.9导入容器快照

```
cat ubuntu.tar | docker import - ubuntu:bk
```

或者

```bash
docker import http://example.com/exampleimage.tgz example/imagerepo
```

#### 3.10删除容器

```bash
docker rm -f <容器ID>
```

### 4.端口映射

#### 4.1指定端口映射

可以通过 -p 参数来设置不一样的端口

```bash
docker run -d -p 5000:5000 training/webapp python app.py
```

可以通过 127.0.0.1:5000:5000 指定IP，默认绑定的都是TCP端口，如果需要绑定UDP端口，127.0.0.1:5000:5000/udp。

#### 4.2查看端口映射

```bash
docker port <容器ID>
```

### 5.获取容器状态

#### 5.1查看应用程序日志

```bash
docker logs -f <容器ID>
```

-f 可以让docker logs 像使用tail -f 一样来输出容器内部的标准输出

#### 5.2查看应用程序进程

```bash
docker top <容器ID>
```

5.3查看容器配置和状态信息

```bash
docker inspect <容器ID>
```

### 6.查找镜像

```bash
docker search <镜像名>
```

### 7.删除镜像

```bash
docker rmi <镜像名>
```

### 8.提交更改后的镜像

```bash
docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2
```

各个参数说明：

- **-m:** 提交的描述信息
- **-a:** 指定镜像作者
- **e218edb10161：**容器 ID
- **runoob/ubuntu:v2:** 指定要创建的目标镜像名