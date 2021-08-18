---
layout:     post title:      SpringBoot学习记录 subtitle:   SpringBoot配置文件、日志框架 date:       2021-08-17 author:     byo
header-img: img/the-first.png catalog:   true tags:

- spring

---

# SpringBoot基础概念

通过Spring Boot，可以轻松地创建独立的，基于生产级别的基于Spring的应用程序，并且可以 “运行”它们；其实Spring Boot 的设计是为了让你尽可能快的跑起来 Spring 应用程序并且尽可能减少你的配置文件。

## SpringBoot的基本特性

* SpringBoot Starter：他将常用的依赖分组进行了整合，将其合并到一个依赖中，这样就可以一次 性添加到项目的Maven或Gradle构建中；

* 使编码变得简单，SpringBoot采用 JavaConfig的方式对Spring进行配置，并且提供了大量的注解， 极大的提高了工作效率。

* 自动配置：SpringBoot的自动配置特性利用了Spring对条件化配置的支持，合理地推测应用所需的 bean并自动化配置他们；

* 使部署变得简单，SpringBoot内置了三种Servlet容器，Tomcat，Jetty,undertow.我们只需要一个 Java的运行环境就可以跑SpringBoot的项目了，SpringBoot的项目可以打成一个jar包。

## SpringBoot热部署
###添加依赖
首先添加如下的以下的依赖文件, 并在idea的两个界面中进行开启配置。
```xml
<!-- 引入热部署依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```
###热部署原理
对类加载采用了两种类加载器，对于第三方jar包采用baseclassloader 来加载，
对于开发人员自己开发的代码则使用restartClassLoader来进行加载，这使得比停 掉服务重启要快的多，
因为使用插件只是重启开发人员编写的代码部分。

###排除资源
某些资源在更改后不一定需要触发重新启动。例如，Thymeleaf模板可以就地编辑。
默认情况下，改变 资源/META-INF/maven ， /META-INF/resources ， /resources ， /static ， /public ， 或/templates 不触发重新启动，但确会触发现场重装。
如果要自定义这些排除项，则可以使用该 spring.devtools.restart.exclude 属性。例如，仅排除/static ， /public 
在properties或者yml中设置以下属性：
```yaml
spring.devtools.restart.exclude=static/**,public/**
```
##全局配置文件
Spring Boot使用一个application.properties或者application.yaml的文件作为全局配置文件。
有四个可存放配置文件的位置：
```yaml
–file:./config/
–file:./
–classpath:/config/
–classpath:/
```
翻译成语言如下（按照优先级从高到低的顺序）：
1. 先去项目根目录找config文件夹下找配置文件件
2. 再去根目录下找配置文件
3. 去resources下找config文件夹下找配置文件
4. 去resources下找配置文件

整个设计非常巧妙。SpringBoot会从这四个位置全部加载主配置文件，如果高优先级中配置文件属性与 低优先级配置文件不冲突的属性，则会共同存在— 互补配置。
```
备注：
这里说的配置文件，都还是项目里面。最终都会被打进jar包里面的，需要注意。
1、如果同一个目录下，有application.yml也有application.properties，默认先读取application.properties。
(plus: 仅限spring-boot 2.4.0以前的版本, 之后的版本yaml优先级更高，可通过如下语句调整优先级顺序：
spring.config.use-legacy-processing = true)
2、如果同一个配置属性，在多个配置文件都配置了，默认使用第1个读取到的，后面读取的不覆盖前面读取到的。
3、创建SpringBoot项目时，一般的配置文件放置在“项目的resources目录下”
```