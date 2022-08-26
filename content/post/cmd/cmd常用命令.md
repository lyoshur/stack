---
title: CMD常用命令
description: CMD常用命令
slug: CMD常用命令
date: 2022-03-06 00:00:00+0000
categories:
    - Windows
tags:
    - Windows
---

## cmd常用命令

### 1.判断某个文件是否存在

```bash
IF NOT EXIST "%MAVEN_HOME%\bin\mvn.cmd" ECHO MAVEN_HOME is not defined
```

### 2.删除文件夹

```bash
rd /s /q maven-package
```

### 3.新建文件夹

```bash
md maven-package
```

### 示例

```bash
@ECHO OFF

ECHO ---------------- check maven env --------------------
IF NOT EXIST "%MAVEN_HOME%\bin\mvn.cmd" ECHO MAVEN_HOME is not defined & PAUSE
IF NOT EXIST "%MAVEN_REPO_LOCAL%\settings.xml" ECHO MAVEN_REPO_LOCAL is not defined & PAUSE

:: 删除打包目录
RD /s /q maven-package

:: 生成打包目录
MD maven-package

:: 准备进入打包
ECHO ----------------- ready to package --------------------

:: 对 admin 项目进行打包
CD admin_2
ECHO ------------------ package for admin_2 ---------------------------------------------------------
ECHO ------------------ maven clean ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% clean
TIMEOUT /T 20 /NOBREAK
ECHO ------------------ maven package ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% package
timeout /T 20 /NOBREAK
ECHO ------------------ COPY JAR FILE ---------------------------------------------------------
CD ..
COPY admin_2\target\admin.jar maven-package

:: 对 message_2 项目进行打包
CD message_2
ECHO ------------------ package for message_2 ---------------------------------------------------------
ECHO ------------------ maven clean ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% clean
TIMEOUT /T 20 /NOBREAK
ECHO ------------------ maven package ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% package
timeout /T 20 /NOBREAK
ECHO ------------------ COPY JAR FILE ---------------------------------------------------------
CD ..
COPY message_2\target\message.jar maven-package

:: 对 model 项目进行打包
CD model_2
ECHO ------------------ package for model_2 ---------------------------------------------------------
ECHO ------------------ maven clean ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% clean
TIMEOUT /T 20 /NOBREAK
ECHO ------------------ maven package ---------------------------------------------------------
START %MAVEN_HOME%\bin\mvn.cmd -s %MAVEN_REPO_LOCAL%\settings.xml -Dmaven.repo.local=%MAVEN_REPO_LOCAL% package
timeout /T 20 /NOBREAK
ECHO ------------------ COPY JAR FILE ---------------------------------------------------------
CD ..
COPY model_2\target\model.jar maven-package

EXIT

```

