---
title: spring事务
description: #
slug: spring事务
date: 2022-08-25 14:41:00+0000
categories:
    - Java
tags:
    - Java
---

## spring事务

Spring 之中除了设置事务的「隔离级别」之外，还可以额外配置事务的「传播特性」。

Spring支持以下7种事务传播行为：

- propagation-required：支持当前事务，如果有就加入当前事务中；如果当前方法没有事务，就新建一个事务。

  【note】required 必须的，所以会加入一个事务，加入不了就新建。

- propagation-supports：支持当前事务，如果有就加入当前事务中，如果当前方法没有事务，就以非事务的方法运行。

  【note】supports 支持，有事务支持加入，没有就算了

- propagation-mandatory：支持当前事务，如果有就加入到当前事务，如果房前没有事务，就抛出异常。

  【note】mandatory 强制，当前有事务就算了，要是没有就哭给你看

- propagation-requires_new：新建事务，如果当前存在事务，就把当前事务挂起，如果当前方法不存在事务，就新建事务

  【note】就是要一个新的，旧的不要

- propagation-not-supported：以非事务的方法运行，如果当前方法有事务，则挂起当前事务，如果当前没有事务，则已非事务的方法运行

  【note】不管有没有，我都不用

- propagation-never：以非事务方式运行，如果当前方法存在事务，则抛出异常。如果不存在，就以非事务的方式运行

  【note】我不要，给就哭

- propagation-nested：如果当前方法有事务,则在嵌套事务内执行;如果当前方法没有事务，就新建一个事务

  【note】nested 嵌套

【重要】！！！！在同一个类中，一个方法调用另外一个有注解（比如@Async，@Transational）的方法，注解是不会生效的

在一个Service内部，事务方法之间的嵌套调用，普通方法和事务方法之间的嵌套调用，都不会开启新的事务

