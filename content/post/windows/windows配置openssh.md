---
title: windows配置openssh
description: windows配置openssh
slug: windows配置openssh
date: 2022-11-23 00:00:00+0000
categories:
    - Windows
tags:
    - Windows
---

# windows配置openssh

## 1.安装sshd服务

```bash
powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1
```

## 2.配置开机自启sshd服务

```bash
sc config sshd start= auto
```

## 3.开启服务

```bash
net start sshd
```

## 4.卸载sshd服务

```bash
powershell.exe -ExecutionPolicy Bypass -File uninstall-sshd.ps1
```

## 5.常见问题

- ssh连接被拒绝：openssh文件不能放在桌面！！！！