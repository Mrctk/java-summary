# Log4j2的Filters配置详解

Filters决定日志事件能否被输出。过滤条件有三个值：`ACCEPT(接受)`，`DENY(拒绝)`，`NEUTRAL(中立)`。

## ThresholdFilter

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



## TimeFilter

   时间过滤器可用于将过滤器限制为仅当天的某个部分。

```xml
<Filters>
    <!-- 只允许在每天的 8点~8点半 之间输出日志 -->
    <TimeFilter start="08:00:00" end="08:30:00" onMatch="ACCEPT" onMismatch="DENY" />
</Filters>
```

