---
title: Docker网络
description: #
slug: Docker网络
date: 2022-03-06 00:00:00+0000
categories:
    - Docker
tags:
    - #
---

## Docker网络

### 1.新建Docker网络

```bash
docker network create -d bridge test-net
```

参数说明：

**-d**：参数指定 Docker 网络类型，有 bridge、overlay。

### 2.进行测试

运行两个容器，并加入到docker网络

```bash
docker run -itd --name test1 --network test-net ubuntu /bin/bash
docker run -itd --name test2 --network test-net ubuntu /bin/bash
```

下面通过 ping 来证明 test1 容器和 test2 容器建立了互联关系。

如果 test1、test2 容器内中无 ping 命令，则在容器内执行以下命令安装 ping（即学即用：可以在一个容器里安装好，提交容器到镜像，在以新的镜像重新运行以上俩个容器）。

```bash
apt-get update
apt install iputils-ping
```

### 3.配置DNS

#### 3.1配置全局DNS

我们可以在宿主机的 /etc/docker/daemon.json 文件中增加以下内容来设置全部容器的 DNS：

```bash
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```

配置完，需要重启 docker 才能生效。

#### 3.2在指定容器中设置DNS

```bash
docker run -it --rm -h host_ubuntu  --dns=114.114.114.114 --dns-search=test.com ubuntu
```

**--rm**：容器退出时自动清理容器内部的文件系统。

**-h HOSTNAME 或者 --hostname=HOSTNAME**： 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。

**--dns=IP_ADDRESS**： 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。

**--dns-search=DOMAIN**： 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com。

!! 如果在容器启动时没有指定 **--dns** 和 **--dns-search**，Docker 会默认用宿主主机上的 /etc/resolv.conf 来配置容器的 DNS。