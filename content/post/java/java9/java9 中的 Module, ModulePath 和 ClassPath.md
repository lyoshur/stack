---
title: java9 中的 Module, ModulePath 和 ClassPath
description: java9 中的 Module, ModulePath 和 ClassPath
slug: java9 中的 Module, ModulePath 和 ClassPath
date: 2022-03-09 00:00:00+0000
categories:
    - Java
tags:
    - JAVA
---


# java9 中的 Module, ModulePath 和 ClassPath

我们都知道，从 Java9 开始，引入了模块系统，提供了对大型复杂系统很重要的隔离，依赖管理等特性。

首先是个吐槽。

作为Java9的主要特性，抛开JDK本身的需求，模块系统的设计有不少可以商榷的地方，也引起了不小的争议，甚至一度被JCP否决；对用户来说，迁移成本高，收益不明显，为了这个项目花了三年半的时间，Block了其他项目的进展，让本来已经老旧的Java显得更加老旧，实在是不明智啊。

下面是正文开始。

一个Jar包，如果根目录下有 module-info.class 这个文件，则是一个已经包含了模块化信息的模块。在 module-info.class 中，可以指定此模块的名字和版本，哪些 Package 可以被别的模块访问，依赖于哪些模块，这些依赖是否继续传递等。

```java
module net.dongliu.serviceimpl {

    requires net.dongliu.utils;

    requires net.dongliu.service;

    exports net.dongliu.serviceimpl.api;

    provides net.dongliu.service.MyService

    with net.dongliu.serviceimpl.MyServiceImpl;

}
```

旧项目的兼容

然而在此之前已经发布的Jar包，和开发的项目，都是没有模块化的，Java9 如何处理这些Jar包和项目呢? 这就要靠新引入的 ModulePath了，简单的说就是老人老办法，新人新办法，对 ClassPath 和 ModulePath 采用不同的规则。

ModulePath 和 ClassPath

ModulePath 的概念和ClassPath 类似，不过 ModulePath 中的 Jar 包或 Jmod 文件被当作 Module 来处理，而 ClassPath 中的的 Jar 包，无论是否模块化都会被当作传统 Jar 包处理。

Java9 制定了以下规则以保证旧 Jar 包和项目可以无缝的迁移到新的 Java 版本上:

所有 ClassPath 下的 Jar 包，Class，资源文件等都在一个 Unnamed Module 中

Unnamed Module 的 Class 可以看到和使用所有 Module Path 中导出的 Package，所有 Class Path 中的 Package，以及所有 JDK 系统模块的 Package

ModulePath 下普通模块中的 Class 只能看到 module-info 中定义的依赖模块中导出的 Package，也看不到 Unnamed Module 中的内容。

根据这些规则，如果项目还是使用 ClassPath，则对项目来说没有影响，项目也不需要定义模块依赖。另外，项目的依赖可以放在 ClassPath 上，也可以放在 ModulePath 上，区别是放在 ModulePath 上的话不能使用模块中未标记为导出的 Package。

这里需要解释一下 Unnamed Module。Unnamed Module 是一个特殊的，自动生成的 Module，所有 ClassPath 下的内容在 Java9 中都是挂在 Unnamed Module 名下的。对于同一个 ClassLoader，只有一个 Unnamed Module。

模块化项目使用未模块化的 Jar 包

现在对于未模块化的传统项目没问题了，那么，如果要创建一个模块化的项目，但是依赖的 Jar 包还没有模块化怎么办？按照上面的规则，因为这些 Jar 包没有模块信息，所以就没法在项目的 module-info 中定义这个依赖。为了保证模块化的新项目能够使用尚未模块化的旧 Jar 包，又定义了 Automatic Module 的概念。

Automatic Module

一个不包含 module-info.class 的传统 Jar 包，如果放到了 ModulePath 下，就变成了一个 Automatic Module。

一个 Automatic Module:

默认的依赖于所有 ModulePath 中的模块，可以访问所有模块中导出的 Package。

默认导出此模块中的所有 Package。

如果 Jar 包在 MetaInfo 文件中定义了 Automatic-Module-Name，则使用这个值作为模块的名称；如果没有定义，那么使用 Jar 包的文件名去掉扩展名的那部分作为模块名，其中包含的-要替换成.，因为模块名不允许包含-字符。

按照如上规则，项目中就可以把一个未模块化的 Jar 包，当成一个模块来使用了。

Automatic Module 的争议

Jigsaw 这个项目，其中最有争议的是如何自动给模块命名，也就是上面的第三个规则。Jigsaw 项目的 JSR 投票第一次被否决，除了 IBM，JBoss 这样因为有自己的模块系统而处于政治原因投反对票的之外，基本上都是由于反对这个自动命名规则。现在的项目基本是采用 Maven，Gradle 等组织构建，这些项目最后生成的 Jar 包文件名只包含 artifactId，用这个做模块名明显是不合适的。更多的人希望用 groupId + artifactId 来作为模块——这个是可行的，因为 Maven 项目生成的 Jar 包会在 META-INF/maven 下保存项目的元信息。可惜的是最后仍然是没有采用这个方案。

所以为了保证将来平滑的迁移，如果你是一个模块的维护者，可以在 MANIFEST.MF 中加一个Automatic-Module-Name 的值。 比如使用 Maven 的话可以这样指定:

maven-jar-plugin

net.dongliu.service