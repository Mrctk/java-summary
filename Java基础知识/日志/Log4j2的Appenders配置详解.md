# Log4j2的Appenders分类与配置详解

Appenders是输出源，用于定义日志输出的地方。
log4j2支持的输出源有很多，有控制台ConsoleAppender、文件FileAppender、AsyncAppender、RandomAccessFileAppender、RollingFileAppender、RollingRandomAccessFile 等

[TOC]

## ConsoleAppender

控制台输出源是将日志打印到控制台上，开发的时候一般都会配置，以便调试。

- name：指定Appender的名字。
- target：SYSTEM_OUT 或 SYSTEM_ERR,一般只设置默认:SYSTEM_OUT。
- PatternLayout：输出格式，不设置默认为:%m%n。

## AsyncAppender

异步输出。AsyncAppender接受对其他Appender的引用，并使LogEvents在单独的Thread上写入它们。

默认情况下，AsyncAppender使用 [java.util.concurrent.ArrayBlockingQueue](http://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ArrayBlockingQueue.html) ，它不需要任何外部库。请注意，多线程应用程序在使用此appender时应小心：阻塞队列容易受到锁争用的影响，并且我们的 [测试](http://logging.apache.org/log4j/2.x/performance.html#asyncLogging) 表明，当更多线程同时记录时性能可能会变差。考虑使用[无锁异步记录器](http://logging.apache.org/log4j/2.x/manual/async.html)以获得最佳性能。

## FileAppender

文件输出源，用于将日志写入到指定的文件，其底层是一个OutputStreamAppender，需要配置输入到哪个位置（例如：D:/logs/mylog.log）

- name：指定Appender的名字。
- fileName：指定输出日志的目的文件带全路径的文件名。
- PatternLayout：输出格式，不设置默认为:%m%n。

## RollingFileAppender

RollingFileAppender是一个OutputStreamAppender，它写入fileName参数中指定的File，并根据TriggeringPolicy和RolloverPolicy滚动文件。

## RandomAccessFileAppender

RandomAccessFileAppender类似于标准的 [FileAppender，](http://logging.apache.org/log4j/2.x/manual/appenders.html#FileAppender) 除了它总是被缓冲（这不能被关闭），并且在内部它使用 `ByteBuffer + RandomAccessFile` 而不是 `BufferedOutputStream`。与FileAppender相比，我们在[测量中](http://logging.apache.org/log4j/2.x/performance.html#whichAppender)看到“bufferedIO = true”，性能提升了20-200％ 。

## RollingRandomAccessFileAppender

RollingRandomAccessFileAppender类似于标准的 [RollingFileAppender，](http://logging.apache.org/log4j/2.x/manual/appenders.html#RollingFileAppender) 除了它总是被缓冲（这不能被关闭），并且在内部它使用`ByteBuffer + RandomAccessFile` 而不是`BufferedOutputStream`。与RollingFileAppender相比，我们在[测量中](http://logging.apache.org/log4j/2.x/performance.html#whichAppender)看到“bufferedIO = true”，性能提升了20-200％。RollingRandomAccessFileAppender写入fileName参数中指定的文件，并根据TriggeringPolicy和RolloverPolicy滚动文件。

RollingRandomAccessFile: 该输出源也是写入到文件，不同的是比File更加强大，可以指定当文件达到一定大小（如20MB）时，另起一个文件继续写入日志，另起一个文件就涉及到新文件的名字命名规则，因此需要配置文件命名规则。这种方式更加实用，因为你不可能一直往一个文件中写，如果一直写，文件过大，打开就会卡死，也不便于查找日志。

- name：指定Appender的名字。
- fileName 指定当前日志文件的位置和文件名称
- filePattern 指定当发生Rolling时，文件的转移和重命名规则
- immediateFlush 设置为true时 - 默认值，每次写入后都会进行刷新。这将保证数据写入磁盘，但可能会影响性能。
- bufferSize 缓冲区大小，默认为262,144字节（256 * 1024）。
- Policies：指定滚动日志的策略，就是什么时候进行新建日志文件输出日志。
- SizeBasedTriggeringPolicy 指定当文件大小大于size指定的值时，触发Rolling
- TimeBasedTriggeringPolicy 这个配置需要和filePattern结合使用，日期格式精确到哪一位，interval也精确到哪一个单位。注意filePattern中配置的文件重命名规则是${FILE_NAME}-%d{yyyy-MM-dd HH-mm-ss}-%i，最小的时间粒度是ss，即秒钟。TimeBasedTriggeringPolicy默认的size是1，结合起来就是每1秒钟生成一个新文件。如果改成%d{yyyy-MM-dd HH}，最小粒度为小时，则每一个小时生成一个文件
- DefaultRolloverStrategy 指定最多保存的文件个数