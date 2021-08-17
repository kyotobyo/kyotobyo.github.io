---
layout:     post
title:      SpringBoot学习记录
subtitle:   SpringBoot配置文件、日志框架
date:       2021-08-17
author:     byo
header-img: img/the-first.png
catalog:   true
tags:
- spring
---
# SpringBoot基础概念
通过Spring Boot，可以轻松地创建独立的，基于生产级别的基于Spring的应用程序，并且可以 “运行”它们；其实Spring Boot 的设计是为了让你尽可能快的跑起来 Spring 应用程序并且尽可能减少你的配置文件。
## SpringBoot的基本特性
*  SpringBoot Starter：他将常用的依赖分组进行了整合，将其合并到一个依赖中，这样就可以一次
性添加到项目的Maven或Gradle构建中；

* 使编码变得简单，SpringBoot采用 JavaConfig的方式对Spring进行配置，并且提供了大量的注解，
极大的提高了工作效率。

* 自动配置：SpringBoot的自动配置特性利用了Spring对条件化配置的支持，合理地推测应用所需的
bean并自动化配置他们；

* 使部署变得简单，SpringBoot内置了三种Servlet容器，Tomcat，Jetty,undertow.我们只需要一个
Java的运行环境就可以跑SpringBoot的项目了，SpringBoot的项目可以打成一个jar包。

