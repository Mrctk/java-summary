# Log4j2的Policy触发策略与Strategy滚动策略配置详解

[TOC]



`Policy`是用来控制日志文件何时(When)进行滚动的；`Strategy`是用来控制日志文件如何(How)进行滚动的。

如果配置的是`RollingFile`或`RollingRandomAccessFile`，则必须配置一个`Policy`。

## Policy触发策略

### SizeBasedTriggeringPolicy

基于日志文件大小的触发策略。单位有：KB，MB，GB

```
<SizeBasedTriggeringPolicy size="10 MB"/>
```

### CronTriggeringPolicy

基于`Cron`表达式的触发策略，很灵活。

```
<CronTriggeringPolicy schedule="0/5 * * * * ?" />
```

### TimeBasedTriggeringPolicy

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



## Strategy滚动策略

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

