## Redis数据类型

[TOC]



Redis主要有5种数据类型，包括String，List，Set，Zset，Hash，满足大部分的使用要求，Redis各数据类型的使用场景可以参考[Redis使用场景](https://blog.csdn.net/ThinkWon/article/details/101521497)

| 数据类型 |      可以存储的值      |                             操作                             |
| :------: | :--------------------: | :----------------------------------------------------------: |
|  STRING  | 字符串、整数或者浮点数 | 对整个字符串或者字符串的其中一部分执行操作<br />对整数和浮点数执行自增或者自减操作 |
|   LIST   |          列表          | 从两端压入或者弹出元素<br />对单个或者多个元素进行修剪，<br />只保留一个范围内的元素 |
|   SET    |        无序集合        | 添加、获取、移除单个元素<br />检查一个元素是否存在于集合中<br /> 计算交集、并集、差集<br />从集合里面随机获取元素 |
|   HASH   | 包含键值对的无序散列表 | 添加、获取、移除单个键值对<br />获取所有键值对<br /> 检查某个键是否存在 |
|   ZSET   |        有序集合        | 添加、获取、删除元素<br />根据分值范围或者成员来获取元素<br /> 计算一个键的排名 |

### STRING

![string](https://raw.githubusercontent.com/JourWon/image/master/Redis总结/string.png)

```shell
> set hello world
OK
> get hello
"world"
> del hello
(integer) 1
> get hello
(nil)
```

### LIST

![list](https://raw.githubusercontent.com/JourWon/image/master/Redis总结/list.png)

```shell
> rpush list-key item
(integer) 1
> rpush list-key item2
(integer) 2
> rpush list-key item
(integer) 3

> lrange list-key 0 -1
1) "item"
2) "item2"
3) "item"

> lindex list-key 1
"item2"

> lpop list-key
"item"

> lrange list-key 0 -1
1) "item2"
2) "item"
```

### SET

![set](https://raw.githubusercontent.com/JourWon/image/master/Redis总结/set.png)

```shell
> sadd set-key item
(integer) 1
> sadd set-key item2
(integer) 1
> sadd set-key item3
(integer) 1
> sadd set-key item
(integer) 0

> smembers set-key
1) "item"
2) "item2"
3) "item3"

> sismember set-key item4
(integer) 0
> sismember set-key item
(integer) 1

> srem set-key item2
(integer) 1
> srem set-key item2
(integer) 0

> smembers set-key
1) "item"
2) "item3"
```

### HASH

![hash](https://raw.githubusercontent.com/JourWon/image/master/Redis总结/hash.png)

```shell
> hset hash-key sub-key1 value1
(integer) 1
> hset hash-key sub-key2 value2
(integer) 1
> hset hash-key sub-key1 value1
(integer) 0

> hgetall hash-key
1) "sub-key1"
2) "value1"
3) "sub-key2"
4) "value2"

> hdel hash-key sub-key2
(integer) 1
> hdel hash-key sub-key2
(integer) 0

> hget hash-key sub-key1
"value1"

> hgetall hash-key
1) "sub-key1"
2) "value1"
```

### ZSET

![zset](https://raw.githubusercontent.com/JourWon/image/master/Redis总结/zset.png)

```shell
> zadd zset-key 728 member1
(integer) 1
> zadd zset-key 982 member0
(integer) 1
> zadd zset-key 982 member0
(integer) 0

> zrange zset-key 0 -1 withscores
1) "member1"
2) "728"
3) "member0"
4) "982"

> zrangebyscore zset-key 0 800 withscores
1) "member1"
2) "728"

> zrem zset-key member1
(integer) 1
> zrem zset-key member1
(integer) 0

> zrange zset-key 0 -1 withscores
1) "member0"
2) "982"
```

