# Log4j2配置文件详解

[TOC]

## Configuration

根节点，有status和monitorInterval等多个属性

status的值有 “trace”, “debug”, “info”, “warn”, “error” and “fatal”，用于控制log4j2日志框架本身的日志级别，如果将stratus设置为较低的级别就会看到很多关于log4j2本身的日志，如加载log4j2配置文件的路径等信息

monitorInterval，含义是每隔多少秒重新读取配置文件，可以不重启应用的情况下修改配置



## Properties

属性。使用来定义常量，以便在其他配置项中引用，该配置是可选的，例如定义日志的存放位置



## Appenders

Appenders是输出源，用于定义日志输出的地方。
log4j2支持的输出源有很多，有控制台ConsoleAppender、文件FileAppender、AsyncAppender、RandomAccessFileAppender、RollingFileAppender、RollingRandomAccessFile 等

[Log4j2的Appenders配置详解](https://blog.csdn.net/ThinkWon/article/details/101625820)



## Filters

Filters决定日志事件能否被输出。过滤条件有三个值：`ACCEPT(接受)`，`DENY(拒绝)`，`NEUTRAL(中立)`。

[Log4j2的Filters配置详解](https://blog.csdn.net/ThinkWon/article/details/101627162)



## PatternLayout

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



## Policy & Strategy

[Log4j2的Policy触发策略与Strategy滚动策略配置详解](https://blog.csdn.net/ThinkWon/article/details/101628222)



## Loggers

[Log4j2的Loggers配置详解](https://blog.csdn.net/ThinkWon/article/details/101628736)