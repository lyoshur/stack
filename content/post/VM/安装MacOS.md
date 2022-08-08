---
title: 使用VM虚拟机安装黑苹果教程
description: 黑苹果安装
slug: 使用VM虚拟机安装黑苹果教程
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - macos
---

## 黑苹果安装

下载镜像以及破解补丁

### 1.复制破解补丁到VM安装目录

执行安装，成功后会在 tools目录生成

```bash
darwin.iso
darwinPre15.iso
```

### 2.安装镜像，版本选择10.8

### 3.修改macOS.vmx

在配置文件最后，加入以下代码

```bash
smc.version = "0"
cpuid.0.eax = "0000:0000:0000:0000:0000:0000:0000:1011"
cpuid.0.ebx = "0111:0101:0110:1110:0110:0101:0100:0111"
cpuid.0.ecx = "0110:1100:0110:0101:0111:0100:0110:1110"
cpuid.0.edx = "0100:1001:0110:0101:0110:1110:0110:1001"
cpuid.1.eax = "0000:0000:0000:0001:0000:0110:0111:0001"
cpuid.1.ebx = "0000:0010:0000:0001:0000:1000:0000:0000"
cpuid.1.ecx = "1000:0010:1001:1000:0010:0010:0000:0011"
cpuid.1.edx = "0000:0111:1000:1011:1111:1011:1111:1111"
smbios.reflectHost = "TRUE"
hw.model = "MacBookPro14,3"
board-id = "Mac-551B86E5744E2388"
usb_xhci:1.speed = "2"
usb_xhci:1.present = "TRUE"
usb_xhci:1.deviceType = "hub"
usb_xhci:1.port = "1"
usb_xhci:1.parent = "-1"
usb_xhci:3.speed = "4"
usb_xhci:3.present = "TRUE"
usb_xhci:3.deviceType = "hub"
usb_xhci:3.port = "3"
usb_xhci:3.parent = "-1"
keyboard.vusb.enable = "TRUE"
mouse.vusb.enable = "TRUE"
usb:0.present = "TRUE"
usb:0.deviceType = "hid"
usb:0.port = "0"
usb:0.parent = "-1"

```

同时需要将 virtualHW.version = "16" 改成 virtualHW.version = "10"

### 4.USB兼容性配置，切换成USB2.0

### 5.抹除虚拟磁盘

点击磁盘工具，找到VM虚拟磁盘，点击抹除。然后开始安装系统

### 6.安装VMtools

使用CD驱动器加载 破解补丁中的 darwin.iso 进行安装。

```bash
sudo defaults write /Library/Preferences/com.apple.windowserver DisplayResolutionEnabled -bool YES
sudo defaults delete /Library/Preferences/com.apple.windowserver DisplayResolutionDisabled
/Library/Application\ Support/VMware\ Tools/vmware-resolutionSet 3416 1920
```

参考某一个教程，执行了这三条命令，前两条执行正常，最后一个执行报错 unable to find the server

最后在 系统 安全和隐私 启用了 VMTools 重启就正常了，怀疑命令是没啥用

