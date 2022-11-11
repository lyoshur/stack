---
title: MySQL事务
description: #
slug: MySQL事务
date: 2022-08-25 13:36:00+0000
categories:
    - MySQL
tags:
    - MySQL
---

## mysql事务

## 1 事务的ACID

与数据库的事务同时为大众熟知的是ACID。即数据库的原子性(Atomicity)、一致性(Consistency)、隔离性(Isolation)和持久性(Durability)。就像应用内为了线程之间的互斥，线程安全等，需要做大量的工作，数据库为了 ACID，也做了许多的工作。

- 原子性(Atomicity)

  一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被恢复（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

- 一致性(Consistency)

  在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。

- 隔离性(Isolation)

  隔离性还有其他的称呼，如并发控制（concurrency control）、可串行化（serializability）、锁（locking）。事务的隔离性要求每个读写事务的对象与其他事务的操作对象能相互分离，即该事务提交前对其他事务都不可见，这通常使用锁来实现。当前数据库系统中都提供了一种粒度锁（granular lock）的策略，允许事务仅锁住一个实体对象的子集，以此来提高事务之间的并发度。

  数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括:

  - 读未提交（Read uncommitted）
  - 读提交（read committed）
  - 可重复读（repeatable read）
  - 串行化（Serializable）

- 持久性(Durability)

mysql默认的事务隔离级别是 可重复读（repeatable read）。

## 2 事务的分类

从理论的角度来说，可以把事务分为一下几种类型

- 扁平事务（flat transactions）

- 带有保存点的扁平事务（flat transactions with savepoints）

- 链事务（chained transactions）

- 嵌套事务（nested transactions）

- 分布式事务（distributed transactions）

### 2.1 扁平事务

对于扁平事务来说，其隐式地设置了一个保存点，但是在整个事务中，只有这一个保存点，回滚只能回滚到事务开始时的状态。保存点用SAVE WORK函数来建立，通知系统记录当前的处理状态。

### 2.2 带有保存点的扁平事务

允许在事务执行过程中回滚到同一事务中较早的一个状态，这是因为可能某些事务在执行过程中出现的错误并不会对所有的操作都无效，放弃整个事务不合乎要求，开销也太大。保存点（savepoint）用来通知系统应该记住事务当前的状态，以便以后发生错误时，事务能回到该状态。

### 2.3 链事务

链事务可视为保存点模式的一个变种。带有保存点的扁平事务，当发生系统崩溃时，所有的保存点都将消失，因为其保存点是易失的（volatile），而非持久的（persistent）。这意味着当恢复保存点时，事务需要从开始处重新执行，而不能从最近的一个保存点继续执行。

链事务的思想是：在提交一个事务时，释放不需要的数据对象，将必要的处理上下文隐式地传给下一个要开始的事务。注意，提交事务操作和开始下一个事务操作将合并为一个原子操作。这意味着下一个事务将看到上一个事务的结果，就好像在一个事务中进行的。

### 2.4 嵌套事务

嵌套事务是一个层次结构框架。有一个顶层事务（top-level transaction）控制着各个层次的事务。顶层事务之下嵌套的事务被称为子事务（subtransaction），其控制每一个局部的变换。

- 嵌套事务是由若干事务组成的一棵树，子树既可以是嵌套事务，也可以是扁平事务。

- 处在叶节点的事务是扁平事务，但是每个子事务从根到叶节点的距离可以是不同的。

- 位于根节点的事务称为顶层事务，其他事务称为子事务。事务的前驱（predecessor）称为父事务（parent），事务的下一层称为儿子事务（child）。

- 子事务既可以提交也可以回滚，但是它的提交操作并不马上生效，除非由其父事务提交。因此可以推论出，任何子事务都在顶层事务提交后才真正提交。

- 树中的任意一个事务的回滚会引起它的所有子事务一同回滚。故子事务仅保留A、C、I特性，不具有D特性。

在Moss的理论中，实际的工作交由叶节点来完成，即只有叶节点的事务才能访问数据库、发送消息、获取其他类型的资源。而高层的事务仅负责逻辑控制，决定何时调用相关的子事务。即使一个系统不支持嵌套事务，用户也可以通过保存点技术来模拟嵌套事务

### 2.5 分布式事务

分布式事务通常是一个在分布式环境下运行的扁平事务，因此需要根据数据所在位置访问网络中的不同节点。

## 3 事务控制语句

在MySQL命令行的默认设置下，事务都是自动提交（auto commit）的，即执行SQL语句后就会马上执行COMMIT操作。因此要显式地开启一个事务须使用命令BEGIN和START TRANSACTION，或者执行命令SET AUTOCOMMIT=0，以禁用当前会话的自动提交。

- START TRANSACTION | BEGIN：显式地开启一个事务。

- COMMIT：要想使用这个语句的最简形式，只需发出COMMIT。也可以更详细一些，写为COMMIT WORK，不过这二者几乎是等价的。COMMIT会提交事务，并使已对数据库进行的所有修改成为永久性的。

- ROLLBACK：要使用这个语句的最简形式，只需发出ROLLBACK。同样地，也可以写为ROLLBACK WORK，但是二者几乎是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改。

- SAVEPOINT identifier：SAVEPOINT允许在事务中创建一个保存点，一个事务中可以有多个SAVEPOINT。

- RELEASE SAVEPOINT identifier：删除一个事务的保存点，当没有一个保存点执行这句语句时，会抛出一个异常。

- ROLLBACK TO [SAVEPOINT] identifier ：这个语句与SAVEPOINT命令一起使用。可以把事务回滚到标记点，而不回滚在此标记点之前的任何工作。例如可以发出两条UPDATE语句，后面跟一个SAVEPOINT，然后又是两条DELETE语句。如果执行DELETE语句期间出现了某种异常情况，而且捕获到这个异常，并发出ROLLBACK TO SAVEPOINT命令，事务就会回滚到指定的SAVEPOINT，撤销DELETE完成的所有工作，而UPDATE语句完成的工作不受影响。

- SET TRANSACTION：这个语句用来设置事务的隔离级别。InnoDB存储引擎提供的事务隔离级别有READ UNCOMMITTED、READ COMMITTED、REPEATABLE READ和SERIALIZABLE。

START TRANSACTION和BEGIN语句都可以在MySQL命令行下显式地开启一个事务。但是在存储过程中，MySQL数据库的分析器会自动将BEGIN识别为BEGIN ...END，因此在存储过程中只能使用START TRANSACTION语句来开启一个事务。

COMMIT和COMMIT WORK语句基本上是一致的，都用来提交事务。不同之处在于COMMIT WORK用来控制事务结束后的行为是CHAIN还是RELEASE的。如果是CHAIN方式，那么事务就变成了链事务。用户可以通过参数completion_type来进行控制:

- 该参数为0，表示没有任何操作。在这种设置下，COMMIT和COMMIT WORK是完全等价的。

- 当参数completion_type的值为1时，COMMIT WORK等同于COMMIT AND CHAIN，表示马上自动开启一个相同隔离级别的事务

- 参数completion_type为2时，COMMIT WORK等同于COMMIT AND RELEASE。当事务提交后会自动断开与服务器的连接

## 4 事务的隔离级别详解

ISO和ANSI SQL标准制定了四种事务隔离级别的标准，但是很少有数据库厂商遵循这些标准。比如Oracle数据库就不支持READ UNCOMMITTED和REPEATABLE READ的事务隔离级别。

- READ UNCOMMITTED

  READ UNCOMMITTED被称为浏览访问（browse access），仅仅只是对事务而言的

- READ COMMITTED

  READ COMMITTED被称为游标稳定（cursor stability）

- REPEATABLE READ

  REPEATABLE READ是2.9999°的隔离，没有幻读的保护

- SERIALIZABLE

  SERIALIZABLE被称为隔离，或3°的隔离。SQL和SQL2标准的默认事务隔离级别是SERIALIZABLE

InnoDB存储引擎默认的支持隔离级别是REPEATABLE READ，但是与标准SQL不同的是，InnoDB存储引擎在REPEATABLE READ事务隔离级别下，使用Next-Key Lock的锁算法，因此避免了幻读的产生。所以说，InnoDB存储引擎在默认的REPEATABLE READ事务隔离级别下已经能完全保证事务的隔离性要求，即达到SQL标准的SERIALIZABLE隔离级别。

## 5 分布式事务编程

InnoDB存储引擎提供了对于XA事务的支持，并通过XA事务来支持分布式事务的实现。分布式事务指的是允许多个独立的事务资源 （transactional resources） 参与到一个全局的事务中。事务资源通常是关系型数据库系统，也可以是其他类型的资源。全局事务要求在其中的所有参与的事务要么都提交，要么都回滚，这对于事务原有的ACID要求又有了提高。另外，在使用分布式事务时，InnoDB存储引擎的事务隔离级别必须设置为SERIALIZABLE。

XA事务允许不同数据库之间的分布式事务，如一台服务器是MySQL数据库的，另一台是Oracle数据库的，可能还有一台服务器是SQL Server数据库的，只要参与到全局事务中的每个节点都支持XA事务即可。

XA事务由一个或多个资源管理器（resource manager）、一个事务管理器（transaction manager）以及一个应用程序（application program）组成。

- 资源管理器：提供访问事务资源的方法。通常一个数据库就是一个资源管理器。

- 事务管理器：协调参与全局事务中的各个事务。需要和参与到全局事务中的所有资源管理器进行通信。

- 应用程序：定义事务的边界，指定全局事务中的操作。

在MySQL数据库的分布式事务中，资源管理器就是MySQL数据库，事务管理器为连接到MySQL服务器的客户端。

分布式事务使用两段式提交（two-phase commit）的方式。在第一个阶段，所有参与全局事务的节点都开始准备（PREPARE），告诉事务管理器它们准备好提交了。第二个阶段，事务管理器告诉资源管理器执行ROLLBACK还是COMMIT。如果任何一个节点显示不能提交，则所有的节点都被告知需要回滚。可见与本地事务不同的是，需要多一次的PREPARE操作，待收到所有节点的同意信息后，再进行COMMIT或ROLLBACK操作。
