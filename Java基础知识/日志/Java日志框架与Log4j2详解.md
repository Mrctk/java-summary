

# Java日志框架与Log4j2详解

在项目开发中，都不可避免的使用到日志。没有日志虽然不会影响项目的正确运行，但是没有日志的项目可以说是不完整的。日志在调试，错误或者异常定位，数据分析中的作用是不言而喻的。



## 日志作用

### 调试

在Java项目调试时，查看栈信息可以方便地知道当前程序的运行状态，输出的日志便于记录程序在之前的运行结果。如果你大量使用System.out或者System.err，这是一种最方便最有效的方法，但显得不够专业。

### 错误定位

不要以为项目能正确跑起来就可以高枕无忧，项目在运行一段时候后，可能由于数据问题，网络问题，内存问题等出现异常。这时日志可以帮助开发或者运维人员快速定位错误位置，提出解决方案。

### 数据分析

大数据的兴起，使得大量的日志分析成为可能，ELK也让日志分析门槛降低了很多。日志中蕴含了大量的用户数据，包括点击行为，兴趣偏好等，用户画像对于公司下一步的战略方向有一定指引作用。



既然日志在我们的项目中的作用这么重要，那么我们就得好好了解一下有哪些日志框架，并运用到实际的项目开发中。小编主要是做Java开发的，这里就说说常用的Java日志框架。

## 常用日志框架

### Log4j

Log4j是Apache的一个Java的日志库，通过使用Log4j，我们可以控制日志信息输送的目的地（控制台、文件、数据库等）；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。

### Logback

Logback，一个“可靠、通用、快速而又灵活的Java日志框架”。logback当前分成三个模块：logback-core，logback- classic和logback-access。logback-core是其它两个模块的基础模块。logback-classic是log4j的一个改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日志系统，如log4j或JDK14 Logging。logback-access模块与Servlet容器（如Tomcat和Jetty）集成，以提供HTTP访问日志功能。请注意，您可以在logback-core之上轻松构建自己的模块。

### Log4j 2

Apache Log4j 2是对Log4j的升级，它比其前身Log4j 1.x提供了重大改进，并提供了Logback中可用的许多改进，同时修复了Logback架构中的一些问题。



现在最优秀的Java日志框架是Log4j2，没有之一。根据官方的测试表明，在多线程环境下，Log4j2的异步日志表现更加优秀。在异步日志中，Log4j2使用独立的线程去执行I/O操作，可以极大地提升应用程序的性能。

在官方的测试中，**Log4j1/Logback/Log4j2**三个日志框架的异步日志性能比较如下图所示。

其中，`Loggers all async`是基于[LMAX Disruptor](http://lmax-exchange.github.com/disruptor/)实现的。可见Log4j2的异步日志性能是最棒的。

![log4j，log4j2，logback异步日志性能比较](https://raw.githubusercontent.com/JourWon/image/master/Java日志框架与Log4j2详解/log4j，log4j2，logback异步日志性能比较.png)



下图比较了Log4j2框架`Sync`、`Async Appenders`和`Loggers all async`三者的性能。其中`Loggers all async`表现最为出色，而且线程数越多，`Loggers all async`性能越好。

![log4j2同步异步Appender比较](https://raw.githubusercontent.com/JourWon/image/master/Java日志框架与Log4j2详解/log4j2同步异步日志性能比较.png)



## 日志门面SLF4J

> 上述介绍的是一些日志框架的实现（Log4j、Logback、log4j2），这里我们需要用日志门面来解决系统与日志实现框架的耦合性。SLF4J，即简单日志门面（Simple Logging Facade for Java），它不是一个真正的日志实现，而是一个抽象层（ abstraction layer），它允许你在后台使用任意一个日志实现。

![slf4j.jpg](https://raw.githubusercontent.com/JourWon/image/master/Java日志框架与Log4j2详解/slf4j.jpg)

前面介绍的几种日志框架一样，每一种日志框架都有自己单独的API，要使用对应的框架就要使用其对应的API，这就大大的增加应用程序代码对于日志框架的耦合性。

使用了SLF4J后，对于应用程序来说，无论底层的日志框架如何变，应用程序不需要修改任意一行代码，就可以直接上线了。



既然Log4j2日志框架性能这么牛逼，那么作为追求卓越的程序猿就得好好学习Log4j2，在项目开发中运用起来。

# Apache Log4j 2详解

## 简介

Apache Log4j 2是对Log4j的升级，它比其前身Log4j 1.x提供了重大改进，并提供了Logback中可用的许多改进，同时修复了Logback架构中的一些问题。



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



## 日志实战(重要)

Log4j 2中记录日志的方式有同步日志和异步日志两种方式，其中异步日志又可分为使用AsyncAppender和使用AsyncLogger两种方式。

### 同步日志

所谓同步日志，即当输出日志时，必须等待日志输出语句执行完毕后，才能执行后面的业务逻辑语句。

下面给出小编在开发中的配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>

    <Properties>
        <!-- 日志输出级别 -->
        <Property name="LOG_INFO_LEVEL" value="info"/>
        <!-- error级别日志 -->
        <Property name="LOG_ERROR_LEVEL" value="error"/>
        <!-- 在当前目录下创建名为log目录做日志存放的目录 -->
        <Property name="LOG_HOME" value="./log"/>
        <!-- 档案日志存放目录 -->
        <Property name="LOG_ARCHIVE" value="./log/archive"/>
        <!-- 模块名称， 影响日志配置名，日志文件名，根据自己项目进行配置 -->
        <Property name="LOG_MODULE_NAME" value="spring-boot"/>
        <!-- 日志文件大小，超过这个大小将被压缩 -->
        <Property name="LOG_MAX_SIZE" value="100 MB"/>
        <!-- 保留多少天以内的日志 -->
        <Property name="LOG_DAYS" value="15"/>
        <!--输出日志的格式：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度， %msg：日志消息，%n是换行符 -->
        <Property name="LOG_PATTERN" value="%d [%t] %-5level %logger{0} - %msg%n"/>
        <!--interval属性用来指定多久滚动一次-->
        <Property name="TIME_BASED_INTERVAL" value="1"/>
    </Properties>

    <Appenders>
        <!-- 控制台输出 -->
        <Console name="STDOUT" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
        </Console>

        <!-- 这个会打印出所有的info级别以上，error级别一下的日志，每次大小超过size或者满足TimeBasedTriggeringPolicy，则日志会自动存入按年月日建立的文件夹下面并进行压缩，作为存档-->
        <RollingRandomAccessFile name="RollingRandomAccessFileInfo"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-infoLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-infoLog-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <!--如果是error级别拒绝，设置 onMismatch="NEUTRAL" 可以让日志经过后续的过滤器-->
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="DENY" onMismatch="NEUTRAL"/>
                <!--如果是info\warn输出-->
                <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，根据当前filePattern设置是1天滚动一次-->
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认同一文件夹下最多保存7个文件-->
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

        <!--只记录error级别以上的日志，与info级别的日志分不同的文件保存-->
        <RollingRandomAccessFile name="RollingRandomAccessFileError"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-errorLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-errorLog-%d{yyyy-MM-dd}-%i.log.gz">
            <Filters>
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

    </Appenders>

    <Loggers>
        <!-- 开发环境使用 -->
        <!--<Root level="${LOG_INFO_LEVEL}">
            <AppenderRef ref="STDOUT"/>
        </Root>-->

        <!-- 测试，生产环境使用 -->
        <Root level="${LOG_INFO_LEVEL}">
            <AppenderRef ref="RollingRandomAccessFileInfo"/>
            <AppenderRef ref="RollingRandomAccessFileError"/>
        </Root>
    </Loggers>

</Configuration>
```



### 混合同步和异步日志

*Log4j-2.9及更高版本在类路径上需要disruptor-3.3.4.jar或更高版本。在Log4j-2.9之前，需要disruptor-3.0.0.jar或更高版本。无需将系统属性“Log4jContextSelector”设置为任何值。*

可以在配置中组合同步和异步记录器。这为您提供了更大的灵活性，但代价是性能略有下降（与使所有记录器异步）相比。使用`<asyncRoot>`或`<asyncLogger>` 配置元素指定需要异步的记录器。配置只能包含一个根记录器（`<root>` 或`<asyncRoot>`元素），但是可以组合异步和非异步记录器。例如，包含`<asyncLogger>`元素的配置文件也可以包含`<root>`和 `<` 同步记录器的元素。

默认情况下，异步记录器不会将[位置](http://logging.apache.org/log4j/2.x/manual/async.html#Location)传递给I / O线程。如果您的某个布局或自定义过滤器需要位置信息，则需要在所有相关记录器的配置中设置“includeLocation = true”，包括根记录器。

首先引入disruptor依赖

```xml
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.2</version>
</dependency>
```



混合异步记录器的配置可能如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>

    <Properties>
        <!-- 日志输出级别 -->
        <Property name="LOG_INFO_LEVEL" value="info"/>
        <!-- error级别日志 -->
        <Property name="LOG_ERROR_LEVEL" value="error"/>
        <!-- 在当前目录下创建名为log目录做日志存放的目录 -->
        <Property name="LOG_HOME" value="./log"/>
        <!-- 档案日志存放目录 -->
        <Property name="LOG_ARCHIVE" value="./log/archive"/>
        <!-- 模块名称， 影响日志配置名，日志文件名，根据自己项目进行配置 -->
        <Property name="LOG_MODULE_NAME" value="spring-boot"/>
        <!-- 日志文件大小，超过这个大小将被压缩 -->
        <Property name="LOG_MAX_SIZE" value="100 MB"/>
        <!-- 保留多少天以内的日志 -->
        <Property name="LOG_DAYS" value="15"/>
        <!--输出日志的格式：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度， %msg：日志消息，%n是换行符 -->
        <Property name="LOG_PATTERN" value="%d [%t] %-5level %logger{0} - %msg%n"/>
        <!--interval属性用来指定多久滚动一次-->
        <Property name="TIME_BASED_INTERVAL" value="1"/>
    </Properties>

    <Appenders>
        <!-- 控制台输出 -->
        <Console name="STDOUT" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
        </Console>

        <!-- 这个会打印出所有的info级别以上，error级别一下的日志，每次大小超过size或者满足TimeBasedTriggeringPolicy，则日志会自动存入按年月日建立的文件夹下面并进行压缩，作为存档-->
        <!--异步日志会自动批量刷新，所以将immediateFlush属性设置为false-->
        <RollingRandomAccessFile name="RollingRandomAccessFileInfo"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-infoLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-infoLog-%d{yyyy-MM-dd}-%i.log.gz"
                                 immediateFlush="false">
            <Filters>
                <!--如果是error级别拒绝，设置 onMismatch="NEUTRAL" 可以让日志经过后续的过滤器-->
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="DENY" onMismatch="NEUTRAL"/>
                <!--如果是info\warn输出-->
                <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，根据当前filePattern设置是1天滚动一次-->
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认同一文件夹下最多保存7个文件-->
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

        <!--只记录error级别以上的日志，与info级别的日志分不同的文件保存-->
        <RollingRandomAccessFile name="RollingRandomAccessFileError"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-errorLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-errorLog-%d{yyyy-MM-dd}-%i.log.gz"
                                 immediateFlush="false">
            <Filters>
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

    </Appenders>

    <Loggers>
        <!-- 开发环境使用 -->
        <!--<Root level="${LOG_INFO_LEVEL}">
            <AppenderRef ref="STDOUT"/>
        </Root>-->

        <!-- 测试，生产环境使用 -->
        <!-- 当使用<asyncLogger> or <asyncRoot>时，无需设置系统属性"Log4jContextSelector" -->
        <AsyncLogger name="com.jourwon" level="${LOG_INFO_LEVEL}" additivity="false">
            <AppenderRef ref="RollingRandomAccessFileInfo"/>
            <AppenderRef ref="RollingRandomAccessFileError"/>
        </AsyncLogger>

        <Root level="${LOG_INFO_LEVEL}">
            <AppenderRef ref="RollingRandomAccessFileInfo"/>
            <AppenderRef ref="RollingRandomAccessFileError"/>
        </Root>
    </Loggers>

</Configuration>
```



### 异步日志(性能最好，推荐使用)

*Log4j-2.9及更高版本在类路径上需要disruptor-3.3.4.jar或更高版本。在Log4j-2.9之前，需要disruptor-3.0.0.jar或更高版本。*

这是最简单的配置，并提供最佳性能。要使所有记录器异步，请将`disruptor` jar添加到类路径，并将系统属性`log4j2.contextSelector`设置 为`org.apache.logging.log4j.core.async.AsyncLoggerContextSelector`。

默认情况下，异步记录器不会将[位置](http://logging.apache.org/log4j/2.x/manual/async.html#Location)传递给I / O线程。如果您的某个布局或自定义过滤器需要位置信息，则需要在所有相关记录器的配置中设置“includeLocation = true”，包括根记录器。

首先引入disruptor依赖

```xml
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.2</version>
</dependency>
```

然后在src/java/resources目录添加log4j2.component.properties配置文件

```properties
# 设置异步日志系统属性
log4j2.contextSelector=org.apache.logging.log4j.core.async.AsyncLoggerContextSelector
```

配置如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>

    <Properties>
        <!-- 日志输出级别 -->
        <Property name="LOG_INFO_LEVEL" value="info"/>
        <!-- error级别日志 -->
        <Property name="LOG_ERROR_LEVEL" value="error"/>
        <!-- 在当前目录下创建名为log目录做日志存放的目录 -->
        <Property name="LOG_HOME" value="./log"/>
        <!-- 档案日志存放目录 -->
        <Property name="LOG_ARCHIVE" value="./log/archive"/>
        <!-- 模块名称， 影响日志配置名，日志文件名，根据自己项目进行配置 -->
        <Property name="LOG_MODULE_NAME" value="spring-boot"/>
        <!-- 日志文件大小，超过这个大小将被压缩 -->
        <Property name="LOG_MAX_SIZE" value="100 MB"/>
        <!-- 保留多少天以内的日志 -->
        <Property name="LOG_DAYS" value="15"/>
        <!--输出日志的格式：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度， %msg：日志消息，%n是换行符 -->
        <Property name="LOG_PATTERN" value="%d [%t] %-5level %logger{0} - %msg%n"/>
        <!--interval属性用来指定多久滚动一次-->
        <Property name="TIME_BASED_INTERVAL" value="1"/>
    </Properties>

    <Appenders>
        <!-- 控制台输出 -->
        <Console name="STDOUT" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
        </Console>

        <!-- 这个会打印出所有的info级别以上，error级别一下的日志，每次大小超过size或者满足TimeBasedTriggeringPolicy，则日志会自动存入按年月日建立的文件夹下面并进行压缩，作为存档-->
        <!--异步日志会自动批量刷新，所以将immediateFlush属性设置为false-->
        <RollingRandomAccessFile name="RollingRandomAccessFileInfo"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-infoLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-infoLog-%d{yyyy-MM-dd}-%i.log.gz"
                                 immediateFlush="false">
            <Filters>
                <!--如果是error级别拒绝，设置 onMismatch="NEUTRAL" 可以让日志经过后续的过滤器-->
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="DENY" onMismatch="NEUTRAL"/>
                <!--如果是info\warn输出-->
                <ThresholdFilter level="${LOG_INFO_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <!--interval属性用来指定多久滚动一次，根据当前filePattern设置是1天滚动一次-->
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认同一文件夹下最多保存7个文件-->
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

        <!--只记录error级别以上的日志，与info级别的日志分不同的文件保存-->
        <RollingRandomAccessFile name="RollingRandomAccessFileError"
                                 fileName="${LOG_HOME}/${LOG_MODULE_NAME}-errorLog.log"
                                 filePattern="${LOG_ARCHIVE}/${LOG_MODULE_NAME}-errorLog-%d{yyyy-MM-dd}-%i.log.gz"
                                 immediateFlush="false">
            <Filters>
                <ThresholdFilter level="${LOG_ERROR_LEVEL}" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <PatternLayout pattern="${LOG_PATTERN}"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="${TIME_BASED_INTERVAL}"/>
                <SizeBasedTriggeringPolicy size="${LOG_MAX_SIZE}"/>
            </Policies>
            <DefaultRolloverStrategy max="${LOG_DAYS}"/>
        </RollingRandomAccessFile>

    </Appenders>

    <Loggers>
        <!-- 开发环境使用 -->
        <!--<Root level="${LOG_INFO_LEVEL}">
            <AppenderRef ref="STDOUT"/>
        </Root>-->

        <!-- 测试，生产环境使用 -->
        <Root level="${LOG_INFO_LEVEL}" includeLocation="false">
            <AppenderRef ref="RollingRandomAccessFileInfo"/>
            <AppenderRef ref="RollingRandomAccessFileError"/>
        </Root>
    </Loggers>

</Configuration>
```

当配置`AsyncLoggerContextSelector`作为异步日志时，请确保在配置中使用普通的 `<root>`和`<logger>`元素。AsyncLoggerContextSelector将确保所有记录器都是异步的，使用的机制与配置`<asyncRoot>` 或`<asyncLogger>`时的机制不同。

通过log.info("是否为异步日志：{}", AsyncLoggerContextSelector.isSelected());可以查看是否为异步日志。



|                | 日志输出方式                                                 |
| -------------- | ------------------------------------------------------------ |
| sync           | 同步打印日志，日志输出与业务逻辑在同一线程内，当日志输出完毕，才能进行后续业务逻辑操作 |
| Async Appender | 异步打印日志，内部采用ArrayBlockingQueue，对每个AsyncAppender创建一个线程用于处理日志输出。 |
| Async Logger   | 异步打印日志，采用了高性能并发框架Disruptor，创建一个线程用于处理日志输出。 |



log4j2.xml文件的配置大致如下

- Configuration
  - Properties
    - Property
  - Appenders
    - Console
       - PatternLayout
    - RollingRandomAccessFile
    - Async
  - Loggers
    - Logger
       - AppenderRef
    - Root
       - AppenderRef




## 配置文件详解

### Configuration

根节点，有status和monitorInterval等多个属性

status的值有 “trace”, “debug”, “info”, “warn”, “error” and “fatal”，用于控制log4j2日志框架本身的日志级别，如果将stratus设置为较低的级别就会看到很多关于log4j2本身的日志，如加载log4j2配置文件的路径等信息

monitorInterval，含义是每隔多少秒重新读取配置文件，可以不重启应用的情况下修改配置



### Properties

属性。使用来定义常量，以便在其他配置项中引用，该配置是可选的，例如定义日志的存放位置



### Appenders

输出源，用于定义日志输出的地方。
log4j2支持的输出源有很多，有控制台ConsoleAppender、文件FileAppender、AsyncAppender、RandomAccessFileAppender、RollingFileAppender、RollingRandomAccessFile 等

#### ConsoleAppender

控制台输出源是将日志打印到控制台上，开发的时候一般都会配置，以便调试。

- name：指定Appender的名字。
- target：SYSTEM_OUT 或 SYSTEM_ERR,一般只设置默认:SYSTEM_OUT。
- PatternLayout：输出格式，不设置默认为:%m%n。

#### AsyncAppender

异步输出。AsyncAppender接受对其他Appender的引用，并使LogEvents在单独的Thread上写入它们。

默认情况下，AsyncAppender使用 [java.util.concurrent.ArrayBlockingQueue](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ArrayBlockingQueue.html) ，它不需要任何外部库。请注意，多线程应用程序在使用此appender时应小心：阻塞队列容易受到锁争用的影响，并且我们的 [测试表明，](http://logging.apache.org/log4j/2.x/performance.html#asyncLogging) 当更多线程同时记录时性能可能会变差。考虑使用无[锁异步记录器](http://logging.apache.org/log4j/2.x/manual/async.html)以获得最佳性能。

#### FileAppender

文件输出源，用于将日志写入到指定的文件，其底层是一个OutputStreamAppender，需要配置输入到哪个位置（例如：D:/logs/mylog.log）

- name：指定Appender的名字。
- fileName：指定输出日志的目的文件带全路径的文件名。
- PatternLayout：输出格式，不设置默认为:%m%n。

#### RollingFileAppender

RollingFileAppender是一个OutputStreamAppender，它写入fileName参数中指定的File，并根据TriggeringPolicy和RolloverPolicy滚动文件。

#### RandomAccessFileAppender

RandomAccessFileAppender类似于标准的 [FileAppender，](http://logging.apache.org/log4j/2.x/manual/appenders.html#FileAppender) 除了它总是被缓冲（这不能被关闭），并且在内部它使用 `ByteBuffer + RandomAccessFile` 而不是 `BufferedOutputStream`。与FileAppender相比，我们在[测量中](http://logging.apache.org/log4j/2.x/performance.html#whichAppender)看到“bufferedIO = true”，性能提升了20-200％ 。

#### RollingRandomAccessFileAppender

RollingRandomAccessFileAppender类似于标准的 [RollingFileAppender，](http://logging.apache.org/log4j/2.x/manual/appenders.html#RollingFileAppender) 除了它总是被缓冲（这不能被关闭），并且在内部它使用`ByteBuffer + RandomAccessFile` 而不是`BufferedOutputStream`。与RollingFileAppender相比，我们在[测量中](http://logging.apache.org/log4j/2.x/performance.html#whichAppender)看到“bufferedIO = true”，性能提升了20-200％。RollingRandomAccessFileAppender写入fileName参数中指定的文件，并根据TriggeringPolicy和RolloverPolicy滚动文件。

RollingRandomAccessFile: 该输出源也是写入到文件，不同的是比File更加强大，可以指定当文件达到一定大小（如20MB）时，另起一个文件继续写入日志，另起一个文件就涉及到新文件的名字命名规则，因此需要配置文件命名规则。这种方式更加实用，因为你不可能一直往一个文件中写，如果一直写，文件过大，打开就会卡死，也不便于查找日志。

* name：指定Appender的名字。
* fileName 指定当前日志文件的位置和文件名称
* filePattern 指定当发生Rolling时，文件的转移和重命名规则
* immediateFlush 设置为true时 - 默认值，每次写入后都会进行刷新。这将保证数据写入磁盘，但可能会影响性能。
* bufferSize 缓冲区大小，默认为262,144字节（256 * 1024）。
* Policies：指定滚动日志的策略，就是什么时候进行新建日志文件输出日志。
* SizeBasedTriggeringPolicy 指定当文件大小大于size指定的值时，触发Rolling
* TimeBasedTriggeringPolicy 这个配置需要和filePattern结合使用，日期格式精确到哪一位，interval也精确到哪一个单位。注意filePattern中配置的文件重命名规则是${FILE_NAME}-%d{yyyy-MM-dd HH-mm-ss}-%i，最小的时间粒度是ss，即秒钟。TimeBasedTriggeringPolicy默认的size是1，结合起来就是每1秒钟生成一个新文件。如果改成%d{yyyy-MM-dd HH}，最小粒度为小时，则每一个小时生成一个文件
* DefaultRolloverStrategy 指定最多保存的文件个数



### Filters

Filters决定日志事件能否被输出。过滤条件有三个值：`ACCEPT(接受)`，`DENY(拒绝)`，`NEUTRAL(中立)`。

#### ThresholdFilter

输出warn级别一下的日志

```xml
<Filters>
    <!--如果是error级别拒绝，设置 onMismatch="NEUTRAL" 可以让日志经过后续的过滤器-->
    <ThresholdFilter level="error" onMatch="DENY" onMismatch="NEUTRAL"/>
    <!--如果是debug\info\warn级别的日志输出-->
    <ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>
</Filters>
```

只输出error级别以上的日志

```xml
<Filters>
    <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
</Filters>
```



#### TimeFilter

   时间过滤器可用于将过滤器限制为仅当天的某个部分。

```xml
<Filters>
    <!-- 只允许在每天的 8点~8点半 之间输出日志 -->
    <TimeFilter start="08:00:00" end="08:30:00" onMatch="ACCEPT" onMismatch="DENY" />
</Filters>
```



### PatternLayout

用于指定输出日志的格式。详细配置请查看[官网](http://logging.apache.org/log4j/2.x/manual/layouts.html#PatternLayout)

简单示例：

```
<PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS} [%t] %-5level %logger{0} - %msg%n" charset="UTF-8"/>
```

授人以鱼不如授人以渔。关于`pattern`的格式点击http://logging.apache.org/log4j/2.x/manual/layouts.html#PatternLayout

PatternLayout：控制台或文件输出源（Console、File、RollingRandomAccessFile）都必须包含一个PatternLayout节点，用于指定输出文件的格式（如 日志输出的时间 文件 方法 行数 等格式），例如 pattern="%d{yyyy-MM-dd HH:mm:ss,SSS} [%t] %-5level %logger{0} - %msg%n"

```xml
  %d 输出日志日期格式，如%d{yyyy-MM-dd HH:mm:ss,SSS}输出2012-11-02 14:34:02,123
  %t 输出当前线程名称
  %-5level 输出日志级别，-5表示左对齐并且固定输出5个字符，如果不足在右边补空格
  %logger 输出logger名称
  %msg 日志信息
  %n 换行

  其他常用的占位符有：
  %F 输出所在的类文件名，如Log4j2Test.java
  %L 输出行号
  %M 输出所在方法名
  %l 输出语句所在的行数, 包括类名、方法名、文件名、行数
```



### Policy & Strategy

`Policy`是用来控制日志文件何时(When)进行滚动的；`Strategy`是用来控制日志文件如何(How)进行滚动的。

如果配置的是`RollingFile`或`RollingRandomAccessFile`，则必须配置一个`Policy`。

#### Policy触发策略

##### SizeBasedTriggeringPolicy

基于日志文件大小的触发策略。单位有：KB，MB，GB

```
<SizeBasedTriggeringPolicy size="10 MB"/>
```

##### CronTriggeringPolicy

基于`Cron`表达式的触发策略，很灵活。

```
<CronTriggeringPolicy schedule="0/5 * * * * ?" />
```

##### TimeBasedTriggeringPolicy

基于时间的触发策略。该策略主要是完成周期性的log文件封存工作。有两个参数：

interval，integer型，指定两次封存动作之间的时间间隔。这个配置需要和`filePattern`结合使用，`filePattern`日期格式精确到哪一位，interval也精确到哪一个单位。注意`filePattern`中配置的文件重命名规则是%d{yyyy-MM-dd HH-mm-ss}-%i，最小的时间粒度是ss，即秒钟。
TimeBasedTriggeringPolicy默认的size是1，结合起来就是每1秒钟生成一个新文件。如果改成%d{yyyy-MM-dd HH}，最小粒度为小时，则每一个小时生成一个文件

modulate，boolean型，说明是否对封存时间进行调制。若modulate=true， 则封存时间将以0点为边界进行偏移计算。比如，modulate=true，interval=4hours， 那么假设上次封存日志的时间为03:00，则下次封存日志的时间为04:00， 之后的封存时间依次为08:00，12:00，16:00 


简单示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
    <Appenders>
        <RollingRandomAccessFile name="File" fileName="./log/app.log"
                                 filePattern="./log/app-%d{yyyy-MM-dd HH-mm}-%i.log">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss,SSS} [%t] %-5level %logger{0} - %msg%n" charset="UTF-8"/>
            <Policies>
                <!-- 每 5s 翻滚一次 -->
                <!--<CronTriggeringPolicy schedule="0/5 * * * * ?"/>-->
                <!--根据当前filePattern配置，日志文件每3分钟滚动一次-->
                <TimeBasedTriggeringPolicy interval="3"/>
                <!--日志文件大于10 MB滚动一次-->
                <SizeBasedTriggeringPolicy size="10 KB"/>
            </Policies>
            <!--保存日志文件个数-->
            <DefaultRolloverStrategy max="10"/>
        </RollingRandomAccessFile>
    </Appenders>
    <Loggers>
        <Root level="info">
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```



#### Strategy滚动策略

1. DefaultRolloverStrategy

   默认滚动策略

   常用参数：max，保存日志文件的最大个数，默认是7，大于此值会删除旧的日志文件。

   ```xml
   <!--保存日志文件个数-->
   <DefaultRolloverStrategy max="10"/>
   ```

2. DirectWriteRolloverStrategy

   日志直接写入由文件模式表示的文件。

这两个`Strategy`都是控制如何进行日志滚动的，平时大部分用`DefaultRolloverStrategy`就可以了。



### Loggers

**Loggers节点，常见的有两种：Root和Logger**。
`Root`节点用来指定项目的根日志，如果没有单独指定`Logger`，那么就会默认使用该Root日志输出

#### Root

**每个配置都必须有一个根记录器Root**。如果未配置，则将使用默认根LoggerConfig，其级别为ERROR且附加了Console appender。根记录器和其他记录器之间的主要区别是：1.根记录器没有name属性。2.根记录器不支持additivity属性，因为它没有父级。

- level：日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF

- AppenderRef：Root的子节点，用来指定该日志输出到哪个Appender.

#### Logger

Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。

使用`Logger`元素必须有一个name属性，root logger不用name元属性
每个`Logger`可以使用TRACE，DEBUG，INFO，WARN，ERROR，ALL或OFF之一配置级别。如果未指定级别，则默认为ERROR。可以为additivity属性分配值true或false。如果省略该属性，则将使用默认值true。

`Logger`还可以配置一个或多个AppenderRef属性。引用的每个appender将与指定的`Logger`关联。如果在`Logger`上配置了多个appender，则在处理日志记录事件时会调用每个appender。

* name：用来指定该Logger所适用的类或者类所在的包全路径，继承自Root节点。一般是项目包名或者框架的包名，比如：com.jourwon，org.springframework

- level：日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF
- AppenderRef：Logger的子节点，用来指定该日志输出到哪个Appender，如果没有指定，就会默认继承自Root。如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。



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

> lombok就是一个注解工具jar包，能帮助我们省略一繁杂的代码。具体介绍可以看我的博客[Lombok简介、使用、工作原理、优缺点](https://blog.csdn.net/ThinkWon/article/details/86005754)

使用Lombok后，@Slf4j注解生成了log日志常量，无需去声明一个log就可以在类中使用log记录日志。

```java
@Slf4j
public class Log4jTest {
  
    public static void main(String[] args) {
        log.error("Something else is wrong here");
    }
    
}
```
