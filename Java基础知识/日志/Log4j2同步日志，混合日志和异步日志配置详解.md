# Log4j2同步日志，混合日志和异步日志配置详解

Log4j 2中记录日志的方式有同步日志和异步日志两种方式，其中异步日志又可分为使用AsyncAppender和使用AsyncLogger两种方式。

[TOC]

## 同步日志

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



## 混合同步和异步日志

*Log4j-2.9及更高版本在类路径上需要disruptor-3.3.4.jar或更高版本。在Log4j-2.9之前，需要disruptor-3.0.0.jar或更高版本。无需将系统属性“Log4jContextSelector”设置为任何值。*

可以在配置中组合同步和异步记录器。这为您提供了更大的灵活性，但代价是性能略有下降（与使所有记录器异步相比）。使用`<asyncRoot>`或`<asyncLogger>` 配置元素指定需要异步的记录器。配置只能包含一个根记录器（`<root>` 或`<asyncRoot>`元素），但是可以组合异步和非异步记录器。例如，包含`<asyncLogger>`元素的配置文件也可以包含`<root>`和同步记录器的元素。

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



## 异步日志(性能最好，推荐使用)

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

当配置`AsyncLoggerContextSelector`作为异步日志时，请确保在配置中使用普通的 `<root>`和`<logger>`元素。`AsyncLoggerContextSelector`将确保所有记录器都是异步的，使用的机制与配置`<asyncRoot>` 或`<asyncLogger>`时的机制不同。

通过log.info("是否为异步日志：{}", AsyncLoggerContextSelector.isSelected());可以查看是否为异步日志。



|                | 日志输出方式                                                 |
| -------------- | ------------------------------------------------------------ |
| sync           | 同步打印日志，日志输出与业务逻辑在同一线程内，当日志输出完毕，才能进行后续业务逻辑操作 |
| Async Appender | 异步打印日志，内部采用ArrayBlockingQueue，对每个AsyncAppender创建一个线程用于处理日志输出。 |
| Async Logger   | 异步打印日志，采用了高性能并发框架Disruptor，创建一个线程用于处理日志输出。 |

