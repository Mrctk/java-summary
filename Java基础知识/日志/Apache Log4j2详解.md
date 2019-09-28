通过[性能测试](https://blog.csdn.net/ThinkWon/article/details/101621135)得出的结论是Log4j2日志框架性能这么牛逼，那么作为追求卓越的程序猿就得好好学习Log4j2，在项目开发中运用起来。

# Apache Log4j2详解

[TOC]

## 简介

Apache Log4j 2是对Log4j的升级，它比其前身Log4j 1.x提供了重大改进，并提供了Logback中可用的许多改进，同时修复了Logback架构中的一些问题。是目前最优秀的Java日志框架，没有之一。



## 特征

### API分离

Log4j的API与实现分开，使应用程序开发人员可以清楚地了解可以使用哪些类和方法，同时确保向前兼容性。这使Log4j团队能够以安全且兼容的方式进行改进。

### 性能提升

Log4j 2包含基于LMAX Disruptor库的下一代异步记录器。在多线程场景中，异步记录器的吞吐量比Log4j 1.x和Logback高18倍，延迟低。

### 自动重新加载配置

与Logback一样，Log4j 2可以在修改时自动重新加载其配置。与Logback不同，它会在重新配置发生时不会丢失日志事件。

### 高级过滤

与Logback一样，Log4j 2支持基于Log事件中的上下文数据，标记，正则表达式和其他组件进行过滤。此外，过滤器还可以与记录器关联。与Logback不同，您可以在任何这些情况下使用通用的Filter类。

### 插件架构

Log4j使用插件模式配置组件。因此，您无需编写代码来创建和配置Appender，Layout，Pattern Converter等。在配置了的情况下，Log4j自动识别插件并使用它们。

### 无垃圾机制

在稳态日志记录期间，Log4j 2 在独立应用程序中是无[垃圾的](http://logging.apache.org/log4j/2.x/manual/garbagefree.html)，在Web应用程序中是低垃圾。这减少了垃圾收集器的压力，并且可以提供更好的响应性能。



## 使用Log4j2

### 引用依赖

在一般项目中使用Log4j2至少需要引用`log4j-api-2.x`和`log4j-core-2.x`这两个jar包。

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.12.0</version>
</dependency>

<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.12.0</version>
</dependency>
```



在spring boot项目中使用Log4j2

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <!-- 排除spring boot默认日志logback -->
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- 引入log4j2依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

小编在使用中遇到一些坑，同时引入spring-boot-starter和spring-boot-starter-web依赖时，需要在spring-boot-starter中排除spring-boot-starter-logging依赖，因为spring-boot-starter-web依赖于spring-boot-starter，根据Maven依赖关系，在spring-boot-starter中排除spring-boot-starter-logging依赖才能排除成功。



### 添加配置文件

默认情况下，Log4j2在**classpath**下查找名为`log4j2.xml`的配置文件。你也可以使用Java启动命令指定配置文件的全路径。`-Dlog4j.configurationFile=opt/demo/log4j2.xml`，你还可以使用Java代码指定配置文件路径

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.core.LoggerContext;

import java.io.File;

public static void main(String[] args) {

    LoggerContext loggerContext = (LoggerContext) LogManager.getContext(false);
    File file = new File("opt/demo/log4j2.xml");
    loggerContext.setConfigLocation(file.toURI());

}
```



## 同步日志，混合日志和异步日志配置详解

[Log4j2同步日志，混合日志和异步日志配置详解](https://blog.csdn.net/ThinkWon/article/details/101625124)



## 配置文件详解

[Log4j2配置文件详解](https://blog.csdn.net/ThinkWon/article/details/101629302)



## 日志重复打印问题

如果`Root`中的日志包含了`Logger`中的日志信息，并且`AppenderRef`是一样的配置，则日志会打印两次。

这是log4j2继承机制问题，在Log4j2中，logger是有继承关系的，root是根节点，在log4j2中，有个additivity的属性，它是子Logger 是否继承 父Logger 的 输出源（appender） 的属性。具体说，默认情况下子Logger会继承父Logger的appender，也就是说子Logger会在父Logger的appender里输出。若是additivity设为false，则子Logger只会在自己的appender里输出，而不会在父Logger的appender里输出。

要打破这种传递性，也非常简单，在logger中添加 additivity = "false"，如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <Console name="Console">
            <PatternLayout>
                <Pattern>%d{yyyy-MM-dd HH:mm:ss,SSS} [%t] %-5level %logger{0} - %msg%n</Pattern>
            </PatternLayout>
        </Console>
    </Appenders>
    <Loggers>
        <!-- name属性为项目包名或者类名 -->
        <Logger name="com.jourwon" level="debug" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>
        <Root level="error">
            <AppenderRef ref="Console"/>
        </Root>
    </Loggers>
</Configuration>
```



## 使用Lombok工具简化创建Logger类

> lombok就是一个注解工具jar包，能帮助我们省略一繁杂的代码。具体介绍可以看我的博客[Lombok简介、使用、工作原理、优缺点](https://blog.csdn.net/ThinkWon/article/details/101392808)

使用Lombok后，@Slf4j注解生成了log日志常量，无需去声明一个log就可以在类中使用log记录日志。

```java
@Slf4j
public class Log4jTest {
  
    public static void main(String[] args) {
        log.error("Something else is wrong here");
    }
    
}
```

