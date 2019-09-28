# Log4j2的Loggers配置详解

**Loggers节点，常见的有两种：Root和Logger**。
`Root`节点用来指定项目的根日志，如果没有单独指定`Logger`，那么就会默认使用该Root日志输出

## Root

**每个配置都必须有一个根记录器Root**。如果未配置，则将使用默认根LoggerConfig，其级别为ERROR且附加了Console appender。根记录器和其他记录器之间的主要区别是：1.根记录器没有name属性。2.根记录器不支持additivity属性，因为它没有父级。

- level：日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF
- AppenderRef：Root的子节点，用来指定该日志输出到哪个Appender.

## Logger

Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。

使用`Logger`元素必须有一个name属性，root logger不用name元属性
每个`Logger`可以使用TRACE，DEBUG，INFO，WARN，ERROR，ALL或OFF之一配置级别。如果未指定级别，则默认为ERROR。可以为additivity属性分配值true或false。如果省略该属性，则将使用默认值true。

`Logger`还可以配置一个或多个AppenderRef属性。引用的每个appender将与指定的`Logger`关联。如果在`Logger`上配置了多个appender，则在处理日志记录事件时会调用每个appender。

- name：用来指定该Logger所适用的类或者类所在的包全路径，继承自Root节点。一般是项目包名或者框架的包名，比如：com.jourwon，org.springframework

- level：日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF
- AppenderRef：Logger的子节点，用来指定该日志输出到哪个Appender，如果没有指定，就会默认继承自Root。如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。

