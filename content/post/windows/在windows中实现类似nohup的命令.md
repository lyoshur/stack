---
title: 在windows中实现类似nohup的命令
description: 在windows中实现类似nohup的命令
slug: 在windows中实现类似nohup的命令
date: 2022-11-23 00:00:00+0000
categories:
    - Windows
tags:
    - Windows
---


# 在windows中实现类似nohup的命令

## 0.需求背景

在项目中遇到了，因为某些特殊原因windows自带的远程桌面无法使用，故使用openssh连接windows。

但这时遇到一个问题。在使用终端启动某些服务后，当终端断开连接，服务也随之关闭。

所以希望在windows上实现类似nohup的命令。以下是测试的方法。

## 1.使用start命令（不满足）

将原来的命令

```bash
cmd.exe /c call start.bat
```

这时日志会输出到终端，同时终端被阻塞。无法进行其他操作。

修改为

```bash
start /B /I /MIN cmd.exe /c call start.bat
```

这时日志会输出到终端，终端不被阻塞，但是断开终端使，启动的服务同样会关闭。

## 2.使用git中的nohup（不满足）

### 2.1直接启动jar（不满足）

```bash
bash.exe java -jar C:\Users\lyoshur\Desktop\demo_jar\demo.jar
```

报错 cannot execute binary file 

### 2.2使用bash启动bat（不满足）

```bash
bash C:\Users\lyoshur\Desktop\start.bat

C:\Users\lyoshur\Desktop\start.bat: line 1: cd: C:UserslyoshurDesktopdemo_jar: No such file or directory
Error: Unable to access jarfile demo.jar
```

### 2.3使用bash启动bat（不满足）

```
bash C:\Users\lyoshur\Desktop\demo_jar\start.bat
Error: Unable to access jarfile demo.jar
```

## 3.使用自定义程序（满足,但有延迟大概一分钟）

使用一个自己写的程序，接收要启动的脚本，然后通过程序调用任务计划来执行对应的脚本。这样可以忽略掉终端停止信号。

```go
package main

import (
	"gitee.com/lyoshur/golog"
	"gitee.com/lyoshur/gutils/crypto"
	"os"
	"os/exec"
	"strconv"
	"time"
)

func main() {
	// 获取临时文件所在目录
	tempPath := os.Getenv("TEMP") + "\\" + "runCMD\\"
	// 初始化日志
	logger := golog.GetLogger()
	logger.AddHandler(golog.GetPrintHandler())
	logger.AddHandler(golog.GetFileHandler(tempPath))

	logger.Debug("日志输出目录：" + tempPath)
	if len(os.Args) != 2 {
		logger.Error("参数数量不对：")
		for _, value := range os.Args {
			logger.Error("参数：" + value)
		}
		return
	}

	// 解析路径
	cmdPath := os.Args[1]
	logger.Debug("待执行的CMD文件为：" + cmdPath)

	// 计算MD5值
	taskName := crypto.EncodeMD5(os.Args[1])
	logger.Debug("待执行的CMD文件路径MD5为：" + taskName)

	// 获取当前时间
	nextMinute := 60 - time.Now().Second()
	runTime := time.Now().Add(time.Duration(nextMinute+6) * time.Second)
	hour := runTime.Hour()
	min := runTime.Minute()
	second := runTime.Second()
	runTimeStr := strconv.Itoa(hour + 100)[1:] + ":" + strconv.Itoa(min + 100)[1:] + ":" + strconv.Itoa(second + 100)[1:]
	logger.Debug("正在启动脚本：" + runTimeStr)
    
	err := exec.Command(
		"schtasks",
		"/delete",
		"/f",
		"/tn",
		taskName,
	).Start()
	err = exec.Command(
		"schtasks",
		"/create",
		"/sc",
		"once",
		"/st",
		runTimeStr,
		"/tn",
		taskName,
		"/tr",
		cmdPath,
	).Start()
	if err != nil {
		logger.Error(err.Error())
		return
	}
}

```

- 首先是计划任务，必须将启动时间设置到下一分钟，否则不会启动。这里怀疑和计划任务的时间轮刻度有关
- 计划任务必须在新建任务前，删除掉旧任务，否则新建任务不会覆盖原有任务。（主键为 tn）
- 计划任务的时间为 00:00:00 不能出现省略 不能出现12:1:55
- 执行命令时，进行使用命令加参数的分隔形式。直接拼成一整个字符串，可能导致启动失败。

## 4.使用自定义程序（未验证）

这里有一种思路，注册一个服务，这个服务不停轮询一个文件，有启动命令就执行，然后添加一个自定义命令，往那个文件中添加记录。优点是没有延迟。缺点是需要处理好文件读写锁以及多线程。同时对环境要求比较苛刻，要加一个自启动的服务和一个自定义命令。

## 5.使用vbs来创建新的线程（未验证）



## 6.使用powershell来创建新的线程（不满足）

```bash
powershell Start-Process
```

