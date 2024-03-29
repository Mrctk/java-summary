# Redis总结

![Redis Logo](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzL1JlZGlzJUU1JTlCJUJFJUU2JUEwJTg3LmpwZw)

[toc]



## Redis简介

Redis 是一个使用 C 语言编写的，开源的（BSD许可）高性能非关系型（NoSQL）的键值对数据库。

Redis 可以存储键和五种不同类型的值之间的映射。键的类型只能为字符串，值支持五种数据类型：字符串、列表、集合、散列表、有序集合。

与传统数据库不同的是 Redis 的数据是存在内存中的，所以读写速度非常快，因此 redis 被广泛应用于缓存方向，每秒可以处理超过 10万次读写操作，是已知性能最快的Key-Value DB。另外，Redis 也经常用来做分布式锁。除此之外，Redis 支持事务 、持久化、LUA脚本、LRU驱动事件、多种集群方案。

从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助。



## Redis的优缺点

### 优点

* 读写性能优异， Redis能读的速度是110000次/s，写的速度是81000次/s。
* 支持数据持久化，支持AOF和RDB两种持久化方式。
* 支持事务，Redis的所有操作都是原子性的，同时Redis还支持对几个操作合并后的原子性执行。
* 数据结构丰富，除了支持string类型的value外还支持hash、set、zset、list等数据结构。
* 支持主从复制，主机会自动将数据同步到从机，可以进行读写分离。

 

### 缺点

* 数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
* Redis 不具备自动容错和恢复功能，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的IP才能恢复。
* 主机宕机，宕机前有部分数据未能及时同步到从机，切换IP后还会引入数据不一致的问题，降低了系统的可用性。
* Redis 较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。



## 数据类型
Redis主要有5种数据类型，包括String，List，Set，Zset，Hash，满足大部分的使用要求
具体可以参考[Redis数据类型](https://blog.csdn.net/ThinkWon/article/details/101521724)



## 使用场景

由于Redis优异的读写性能，持久化支持等优势，Redis的使用场景非常多，主要包括计数器，缓存，消息队列，分布式锁等，具体使用场景可以参考[Redis使用场景](https://blog.csdn.net/ThinkWon/article/details/101521497)



## 持久化

Redis 是内存型数据库，为了之后重用数据（比如重启机器、机器故障之后回复数据），或者是为了防止系统故障而将数据备份到一个远程位置，需要将内存中的数据持久化到硬盘上。

Redis 提供了RDB和AOF两种持久化方式。默认是只开启RDB，当Redis重启时，它会优先使用AOF文件来还原数据集。

Redis持久化详解可以参考[Redis持久化](https://blog.csdn.net/ThinkWon/article/details/101522209)



## 过期键的删除策略

Redis中有个设置时间过期的功能，即对存储在 redis 数据库中的值可以设置一个过期时间。作为一个缓存数据库，这是非常实用的。如我们一般项目中的 token 或者一些登录信息，尤其是短信验证码都是有时间限制的，按照传统的数据库处理方式，一般都是自己判断过期，这样无疑会严重影响项目性能。

Redis有三种不同的删除策略：立即删除，惰性删除，定时删除

具体可以参考[Redis过期键的删除策略](https://blog.csdn.net/ThinkWon/article/details/101522970)



但是仅仅通过设置过期时间还是有问题的。我们想一下：如果定期删除漏掉了很多过期 key，然后你也没及时去查，也就没走惰性删除，此时会怎么样？如果大量过期key堆积在内存里，导致redis内存块耗尽了。怎么解决这个问题呢？ **redis 数据淘汰策略。**

## 数据淘汰策略

可以设置内存最大使用量，当内存使用量超出时，会施行数据淘汰策略。

数据淘汰策略详解可参考[Redis数据淘汰策略](https://blog.csdn.net/ThinkWon/article/details/101530624)



## Redis与Memcached的区别

两者都是非关系型内存键值数据库，现在公司一般都是用 Redis 来实现缓存，而且 Redis 自身也越来越强大了！Redis 与 Memcached 的区别请参考[Redis与Memcached的区别](https://blog.csdn.net/ThinkWon/article/details/101530406)



## 事务

Redis 通过 MULTI、EXEC、WATCH 等命令来实现事务(transaction)功能。事务提供了一种将多个命令请求打包，然后一次性、按顺序地执行多个命令的机制，并且在事务执行期间，服务器不会中断事务而改去执行其他客户端的命令请求，它会将事务中的所有命令都执行完毕，然后才去处理其他客户端的命令请求。

事务中的多个命令被一次性发送给服务器，而不是一条一条发送，这种方式被称为流水线，可以减少客户端与服务器之间的网络通信次数从而提升性能。

在传统的关系式数据库中，常用 ACID 性质来检验事务功能的可靠性和安全性。在 Redis 中，事务总是具有原子性（Atomicity）、一致性（Consistency）和隔离性（Isolation），并且当 Redis 运行在某种特定的持久化模式下时，事务也具有持久性（Durability）。





## 事件

Redis 服务器是一个事件驱动程序。

### 文件事件

服务器通过套接字与客户端或者其它服务器进行通信，文件事件就是对套接字操作的抽象。

Redis 基于 Reactor 模式开发了自己的网络事件处理器，使用 I/O 多路复用程序来同时监听多个套接字，并将到达的事件传送给文件事件分派器，分派器会根据套接字产生的事件类型调用相应的事件处理器。

![文件事件](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNiU5NiU4NyVFNCVCQiVCNiVFNCVCQSU4QiVFNCVCQiVCNi5wbmc)

### 时间事件

服务器有一些操作需要在给定的时间点执行，时间事件是对这类定时操作的抽象。

时间事件又分为：

- 定时事件：是让一段程序在指定的时间之内执行一次
- 周期性事件：是让一段程序每隔指定时间就执行一次

目前Redis只使用周期性事件，而没有使用定时事件。 一个事件时间主要由三个属性组成：

1. id：服务器为时间事件创建的全局唯一ID
2. when：毫秒精度的UNIX时间戳，记录了时间事件的到达时间
3. timeProc：时间事件处理器，一个函数

实现服务器将所有时间事件都放在一个无序链表中，每当时间事件执行器运行时，遍历整个链表，查找所有已到达的时间事件，并调用相应的事件处理器。（该链表为无序链表，不按when属性的大小排序）

### 事件的调度与执行

服务器需要不断监听文件事件的套接字才能得到待处理的文件事件，但是不能一直监听，否则时间事件无法在规定的时间内执行，因此监听时间应该根据距离现在最近的时间事件来决定。

![事件处理流程](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNCVCQSU4QiVFNCVCQiVCNiVFNSVBNCU4NCVFNyU5MCU4NiVFNiVCNSU4MSVFNyVBOCU4Qi5wbmc)



## Sentinel

Sentinel（哨兵）可以监听集群中的服务器，并在主服务器进入下线状态时，自动从从服务器中选举出新的主服务器。





## 分片

分片是将数据划分为多个部分的方法，可以将数据存储到多台机器里面，这种方法在解决某些问题时可以获得线性级别的性能提升。

假设有 4 个 Redis 实例 R0，R1，R2，R3，还有很多表示用户的键 user:1，user:2，... ，有不同的方式来选择一个指定的键存储在哪个实例中。

- 最简单的方式是范围分片，例如用户 id 从 0\~1000 的存储到实例 R0 中，用户 id 从 1001\~2000 的存储到实例 R1 中，等等。但是这样需要维护一张映射范围表，维护操作代价很高。
- 还有一种方式是哈希分片，使用 CRC32 哈希函数将键转换为一个数字，再对实例数量求模就能知道应该存储的实例。

根据执行分片的位置，可以分为三种分片方式：

- 客户端分片：客户端使用一致性哈希等算法决定键应当分布到哪个节点。
- 代理分片：将客户端请求发送到代理上，由代理转发请求到正确的节点上。
- 服务器分片：Redis Cluster。





## 复制

通过使用 slaveof host port 命令来让一个服务器成为另一个服务器的从服务器。

一个从服务器只能有一个主服务器，并且不支持主主复制。

### 连接过程

1. 主服务器创建快照文件，发送给从服务器，并在发送期间使用缓冲区记录执行的写命令。快照文件发送完毕之后，开始向从服务器发送存储在缓冲区中的写命令
2. 从服务器丢弃所有旧数据，载入主服务器发来的快照文件，之后从服务器开始接受主服务器发来的写命令
3. 主服务器每执行一次写命令，就向从服务器发送相同的写命令

### 主从链

随着负载不断上升，主服务器可能无法很快地更新所有从服务器，或者重新连接和重新同步从服务器将导致系统超载。为了解决这个问题，可以创建一个中间层来分担主服务器的复制工作。中间层的服务器是最上层服务器的从服务器，又是最下层服务器的主服务器。

![主从链](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNCVCOCVCQiVFNCVCQiU4RSVFOSU5MyVCRS5wbmc)





## 一个简单的论坛系统

该论坛系统功能如下：

- 可以发布文章
- 可以对文章进行点赞
- 在首页可以按文章的发布时间或者文章的点赞数进行排序显示

### 文章信息

文章包括标题、作者、赞数等信息，在关系型数据库中很容易构建一张表来存储这些信息，在 Redis 中可以使用 HASH 来存储每种信息以及其对应的值的映射。

Redis 没有关系型数据库中的表这一概念来将同种类型的数据存放在一起，而是使用命名空间的方式来实现这一功能。键名的前面部分存储命名空间，后面部分的内容存储 ID，通常使用 : 来进行分隔。例如下面的 HASH 的键名为 article:92617，其中 article 为命名空间，ID 为 92617。

![文章信息](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNiU5NiU4NyVFNyVBQiVBMCVFNCVCRiVBMSVFNiU4MSVBRi5wbmc)

### 点赞功能

当有用户为一篇文章点赞时，除了要对该文章的 votes 字段进行加 1 操作，还必须记录该用户已经对该文章进行了点赞，防止用户点赞次数超过 1。可以建立文章的已投票用户集合来进行记录。

为了节约内存，规定一篇文章发布满一周之后，就不能再对它进行投票，而文章的已投票集合也会被删除，可以为文章的已投票集合设置一个一周的过期时间就能实现这个规定。

![点赞功能](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNyU4MiVCOSVFOCVCNSU5RSVFNSU4QSU5RiVFOCU4MyVCRC5wbmc)

### 对文章进行排序

为了按发布时间和点赞数进行排序，可以建立一个文章发布时间的有序集合和一个文章点赞数的有序集合。（下图中的 score 就是这里所说的点赞数；下面所示的有序集合分值并不直接是时间和点赞数，而是根据时间和点赞数间接计算出来的）

![对文章进行排序](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL1JlZGlzJUU2JTgwJUJCJUU3JUJCJTkzLyVFNSVBRiVCOSVFNiU5NiU4NyVFNyVBQiVBMCVFOCVCRiU5QiVFOCVBMSU4QyVFNiU4RSU5MiVFNSVCQSU4Ri5wbmc)