---
title: 3dmax使用Babylon插件导出gltf模型
description: 3dmax使用Babylon插件导出gltf模型
slug: 3dmax使用Babylon插件导出gltf模型
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - 3DMAX
    - Babylon
    - gltf
---

# 3dmax使用Babylon插件导出gltf模型

## 1 需求资源

相关资源整理收集在 3DMAX升级包以及插件-20220708 文件夹中，主要文件包含

Max2015-2019-Babylon-1.3.33[max导出gltf].zip 3dmax2015-2019的插件文件

Max2020-2021Babylon[max导出gltf].rar 3dmax2020-2021的插件文件

3dsMax2020.3_Update.exe 3dmax2020升级2020.3的升级包

## 2 按照版本找到符合当前3DMAX版本的插件文件

将3d max相应对应版本中的文件，拷贝到3dmax的安装目录\bin\assemblies

## 3 点击菜单中新增的 Babylon菜单，选择Babylon File Exporter。对相关参数进行配置后，选择导出文件

## 4 问题汇总

### 4.1 导出选择目录需要选择一个已经存在的目录，否则将提示目录不存在

### 4.2 导出前需要检查插件中的 log页卡，检查插件是否有错误提示