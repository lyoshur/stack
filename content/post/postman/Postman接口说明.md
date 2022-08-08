---
title: Postman使用说明
description: Postman使用说明
slug: Postman使用说明
date: 2022-03-06 00:00:00+0000
categories:
    - Other
tags:
    - Postman
---

# Postman接口说明

## 1 请求头（Header）

### 1.1 HTTP

```
Accept: */*
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN
archOrAppId: 1479265924364341250
Authorization: bearer 19e541a3-2f78-4314-b0e4-6c56e9d57882
Connection: keep-alive
Host: 10.171.136.89:9501
Origin: http://10.171.136.197:8080
Referer: http://10.171.136.197:8080/
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.125 Safari/537.36
```

在HTTP中，请求头是标准的 Key-Value 格式，常用来传递浏览器版本信息，主机Host信息等。

也可以在请求头中，传递用户登录态信息（Authorization）或其他用户自定义的请求头内容（archOrAppId）。

### 1.2 Postman

在Postman中，通常将自定义的请求头信息，放置在【Headers】页卡，Key-Value是必填项。Description是Postman提供的描述信息，并不属于HTTP中传递的内容，在发送请求时，也不会传递。

### 1.3 Spring Boot

当服务需要接受一个特定的请求头时，可以使用【RequestHeader】注解。

```java
@PutMapping("/info")
public R<Boolean> info(@RequestHeader Long archOrAppId){
    return R.ok();
}
```

## 2 地址栏参数（Query Param）

### 2.1 HTTP

地址栏参数是最常见的参数传递方式，第一个参数使用？跟在地址最后，第二个参数开始使用&进行分割

```
http://127.0.0.1/info?a=1&b=2
```

### 2.2 Postman

在Postman中，地址栏参数被放置在【Params】页卡，和请求头类型一致，Key-Value是必填项。Description是Postman提供的描述信息，并不属于HTTP中传递的内容，在发送请求时，也不会传递。

### 2.3  Spring Boot

当接口中的参数，未使用任何注解时，则默认支持从地址栏参数中进行解析。

```java
@PutMapping("/info")
public R<Boolean> info(String a, String b){
    return R.ok();
}
```

## 3 路径参数（Path Param）

### 3.1 HTTP

从最初的设计上，路径参数并不是正统的参数传递方式。其格式为

```
http://127.0.0.1/info/1/2
```

### 3.2 Postman

Postman中并没有单独的页卡，用来传递路径参数。需要手动将参数整理复制到地址栏。需要注意，路径参数是唯一一个参数顺序会影响结果的传递方式。

【/info/1/2】和【/info/2/1】是不同的。

### 3.3 Spring Boot

当服务需要接受一个特定的路径参数时，可以使用【PathVariable】注解。

```java
// {id}表示要解析的参数名 :\\d+ 表示接收参数后 按照 \\d+ 的正则规则进行参数格式验证
@GetMapping("/{id:\\d+}")
public R query(@PathVariable Long id) {
    return R.ok();
}
```

## 4 请求体（Body）

### 4.1 键值对请求体（Form Data）

#### 4.1.1 HTTP

在Post请求中，HTTP请求包含一个请求体，根据类型不同，请求体支持不同的数据格式，键值对请求体（Form Data）是默认的支持格式。

键值对请求体，是原生请求中，唯一支持文件上传的类型。

#### 4.1.2 Postman

在Postman中，键值对请求体放置在【Body】页卡且单选类型选择【form-data】。

默认添加的键值对内容为普通键值对，可以选择Key栏位的下拉框，将值类型改成【File】用来模拟文件上传。

#### 4.1.3 Spring Boot

当需要接收来自Form Data请求中的参数时，可以不使用任何注解。

```java
@PutMapping("/info")
public R<Boolean> info(String a, String b){
    return R.ok();
}
```

！注！在Spring Boot中，当一个接口参数没有任何注解时，默认会从地址栏和Form Data两部分去解析请求中的参数，所以在设计接口时，如果是Get请求，则应使用地址栏参数；而Post请求时，应避免使用地址栏参数，仅使用请求体。避免参数冲突产生歧义。

当前端上传文件时，后端可以使用【MultipartFile】参数类型，来接收一个前端传递的上传文件内容。

```java
@PutMapping("update")
public R update(@RequestParam(value = "file") MultipartFile file) {
	return R.ok();
}
```

如果前端设置了多文件上传，可以使用

```java
@PutMapping("update")
public R update(@RequestParam(value = "files") MultipartFile[] multipartFile) {
	return R.ok();
}
```

### 4.2 JSON请求体（Raw）

#### 4.2.1 HTTP

JSON请求体是指：通过在请求头中额外指定【Content-Type：application/json】，来设置请求体格式放弃使用键值对格式，转而使用纯文本请求体内容。

#### 4.2.2 Postman

在Postman中如果需要使用Json请求体，请求体放置在【Body】页卡且单选类型选择【raw】,并将最右侧下拉选择为【JSON(application/json)】。

#### 4.2.3 Spring Boot

JSON请求体，通常被设计用来传递一个用键值对难以描述，或具备复杂类型的参数结构。

在后端接口，可以使用【RequestBody】注解，来接收一个前端传递的JSON请求体。

```java
public R update(@RequestBody Info info) {
    return R.ok();
}
```

## 5 常见问题汇总

### 5.1 Get、Post、Put、Patch、Delete、Options等请求方式的区别

首先，请求大体分为两类，Get和Post。

Put、Patch、Delete请求，可以当做按照业务规则更细分的Post请求看待。

Get请求的特点是只有请求地址，没有请求体。默认浏览器会记录Get请求。常用于信息获取。

Post请求的特点是具备丰富的请求体类型，浏览器不会记录、不会缓存，常用于信息提交。

| 请求类型 | 请求头（Header） | 地址栏参数（Query Param） | 路径参数（Path Param） | 键值对请求体（Form Data） | JSON请求体（Raw） | 文件上传 |
| -------- | ---------------- | ------------------------- | ---------------------- | ------------------------- | ----------------- | -------- |
| Get      | 支持             | 支持                      | 支持                   | 不支持                    | 不支持            | 不支持   |
| Post     | 支持             | 支持                      | 支持                   | 支持                      | 支持              | 支持     |

Options请求比较特殊，是在正式请求之前进行预请求，用来确定请求的接口，能接受那些请求类型。

### 5.2 请求头（Header）和请求参数的区别

通常情况下请求头中的内容，是对本次请求的整体信息进行描述，常用来保存，本次请求的发起位置，本次请求的请求方式，请求体的参数类型等。

请求参数一般是和业务相关的请求参数，如某个数据表的字段值，要触发某些数据更新等。

