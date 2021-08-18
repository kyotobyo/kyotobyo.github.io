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

* SpringBoot Starter：他将常用的依赖分组进行了整合，将其合并到一个依赖中，这样就可以一次 性添加到项目的Maven或Gradle构建中；

* 使编码变得简单，SpringBoot采用 JavaConfig的方式对Spring进行配置，并且提供了大量的注解， 极大的提高了工作效率。

* 自动配置：SpringBoot的自动配置特性利用了Spring对条件化配置的支持，合理地推测应用所需的 bean并自动化配置他们；

* 使部署变得简单，SpringBoot内置了三种Servlet容器，Tomcat，Jetty,undertow.我们只需要一个 Java的运行环境就可以跑SpringBoot的项目了，SpringBoot的项目可以打成一个jar包。

## SpringBoot热部署

### 添加依赖

首先添加如下的以下的依赖文件, 并在idea的两个界面中进行开启配置。

```xml
<!-- 引入热部署依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

### 热部署原理

对类加载采用了两种类加载器，对于第三方jar包采用baseclassloader 来加载， 对于开发人员自己开发的代码则使用restartClassLoader来进行加载，这使得比停 掉服务重启要快的多，
因为使用插件只是重启开发人员编写的代码部分。

### 排除资源

某些资源在更改后不一定需要触发重新启动。例如，Thymeleaf模板可以就地编辑。 默认情况下，改变 资源/META-INF/maven ， /META-INF/resources ， /resources ， /static ，
/public ， 或/templates 不触发重新启动，但确会触发现场重装。 如果要自定义这些排除项，则可以使用该 spring.devtools.restart.exclude 属性。例如，仅排除/static ， /public
在properties或者yml中设置以下属性：

```yaml
spring.devtools.restart.exclude=static/**,public/**
```

## 全局配置文件

Spring Boot使用一个application.properties或者application.yaml的文件作为全局配置文件。 有四个可存放配置文件的位置：

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

### 全局配置文件中属性注入

举例，如果想为以下两个类注入属性

```java
public class Pet {
    private String type;
    private String name;
    // 省略属性getXX()和setXX()方法
    // 省略toString()方法
}
```

```java

@Component //用于将Person类作为Bean注入到Spring容器中
@ConfigurationProperties(prefix = "person")
//将配置文件中以person开头的属性注入到该类中
public class Person {
    private int id; //id
    private String name; //名称
    private List hobby; //爱好
    private String[] family; //家庭成员
    private Map map;
    private Pet pet; //宠物
    // 省略属性getXX()和setXX()方法
    // 省略toString()方法
}
```

@ConfigurationProperties(prefix = "person")注解的作用是将配置文件中以person开头的属性值通过setXX()方法注入到实体类对应属性中;
@Component注解的作用是将当前注入属性值的Person类对象作为Bean组件放到Spring容器中，只有 这样才能被@ConfigurationProperties注解进行赋值。

随后在pom.xml中添加如下依赖，并重启项目，以便在配置文件中注入属性时，有自动补全提示！

```xml

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

各属性的注入格式如下：

```yaml
#对实体类对象Person进行属性配置
person:
  id: 1
  name: lucy
  hobby: [ 吃饭，睡觉，打豆豆 ]
  family: [ father,mother ]
  map: { k1: v1,k2: v2 }
  pet: { type: dog,name: 旺财 }
```

### 单侧文件属性注入

属性注入常用以下注解：

```
@Configuration：声明一个类作为配置类
@Bean：声明在方法上，将方法的返回值加入Bean容器
@Value：属性注入
@ConfigurationProperties(prefix = "jdbc")：批量属性注入,补充前缀，与全局配置文件中的字段匹配；
@PropertySource("classpath:/jdbc.properties")指定外部属性文件。在类上添加
```

使用举例如下,首先在全局配置文件中作以下声明：

```properties
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://127.0.0.1:3306/springboot_h
jdbc.username=root
jdbc.password=123
```

### 第三方bean的属性注入

首先创建一个其他组件类（模拟是第三方组件包里的bean）：

```java

@Data
public class AnotherComponent {
    private boolean enabled;
    private InetAddress remoteAddress;
}
```

创建MyService注入属性：

```java

@Configuration
public class MyService {
    @ConfigurationProperties("another")
    @Bean
    public AnotherComponent anotherComponent() {
        return new AnotherComponent();
    }
}
```

配置文件：

```properties
another.enabled=true
another.remoteAddress=192.168.10.11
```

## 日志框架

### 统一日志框架使用步骤归纳如下：

1. 排除系统中的其他日志框架。
2. 使用中间包替换要替换的日志框架。
3. 导入我们选择的 SLF4J 实现。

### 日志实例

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

/**
 * 测试日志输出，
 * SLF4J 日志级别从小到大trace,debug,info,warn,error
 *
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class LogbackTest {
    Logger logger = LoggerFactory.getLogger(getClass());

    @Test
    public void testLog() {
        logger.trace("Trace 日志...");
        logger.debug("Debug 日志...");
        logger.info("Info 日志...");
        logger.warn("Warn 日志...");
        logger.error("Error 日志...");
    }
}
```

输出内容如下所示：

```
2020-11-16 19:58:43.094 INFO 39940 --- [ main]
com.lagou.Springboot01DemoApplicationTests : Info 日志...
2020-11-16 19:58:43.094 WARN 39940 --- [ main]
com.lagou.Springboot01DemoApplicationTests : Warn 日志...
2020-11-16 19:58:43.094 ERROR 39940 --- [ main]
com.lagou.Springboot01DemoApplicationTests : Error 日志...
```

已知日志级别从小到大为 trace < debug < info < warn < error . Spring Boot 默认日志级别为 INFO. 由此也可得出Spring Boot的日志格式如下：

```
%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n
# %d{yyyy-MM-dd HH:mm:ss.SSS} 时间
# %thread 线程名称
# %-5level 日志级别从左显示5个字符宽度
# %logger{50} 类名
# %msg%n 日志信息加换行
```

我们也可以在全局配置文件中自定义日志格式：

```properties
# 日志配置
# 指定具体包的日志级别
logging.level.com.lagou=debug
# 控制台和日志文件输出格式
logging.pattern.console=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level
%logger{50} - %msg%n
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}- %msg%n
# 日志输出路径，默认文件spring.log
logging.file.path=spring.log
#logging.file.name=log.log
```

