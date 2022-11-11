---
title: Linux关于curl安装软件时报错
description: #
slug: Linux关于curl安装软件时报错
date: 2022-03-06 00:00:00+0000
categories:
    - Linux
tags:
    - Linux
---

## 关于curl安装软件时报错

执行命令

```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

报错

```bash
curl: relocation error: /lib/x86_64-linux-gnu/libcurl.so.4: symbol GMTLSv1_1_client_method version OPENSSL_1_1_0 not defined in file libssl.so.1.1 with link time reference
```

排查后发现，需要更新 openssl。执行命令

```bash
sudo apt install openssl
```

问题解决。