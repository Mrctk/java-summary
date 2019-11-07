# JSON简介

[toc]



## 概述

JSON(JavaScript Object Notation JavaScript 对象表示法)是一种轻量级的数据交换格式，它基于JavaScript的一个子集，易于人的编写和阅读，也易于机器解析。 JSON采用完全独立于语言的文本格式，但是也使用了类似于C语言家族的习惯（包括C, C++, C#, Java, JavaScript, Perl, Python等）。 这些特性使JSON成为理想的数据交换语言。



## 结构组成

JSON由两种结构组成：

1. 键值对的无序集合——对象(或者叫记录、结构、字典、哈希表、有键列表或关联数组等)
2. 值的有序列表——数组

这些都是常见的数据结构。事实上大部分现代计算机语言都以某种形式支持它们。这使得一种数据格式在同样基于这些结构的编程语言之间交换成为可能。



## 语法规则

- 数据在名称/值对中
- 数据由逗号分隔
- 大括号保存对象
- 中括号保存数组



## JSON的形式

### 对象

一个无序键值对的集合，以"{"开始，同时以"}"结束，键值对之间以":"相隔，不同的键值对之间以","相隔，举例

```json
{
    "key1" : 1,
    "key2" : "string"
}
```

![object](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy81NjU2Njc0LWE0NmQxOWY3MGViMTgwMDkucG5n)



### 数组

值（value）的有序集合。一个数组以“[”（左中括号）开始，“]”（右中括号）结束。值之间使用“,”（逗号）分隔。

举例

```json
[ "Google", "Runoob", "Taobao" ]
```

![array](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy81NjU2Njc0LWUyOGYyZjdlNmE4ZmM4NmIucG5n)





值（value）可以是双引号括起来的字符串（string）、数值(number)、true、false、 null、对象（object）或者数组（array）。这些结构可以嵌套。

![value](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy81NjU2Njc0LTkzMzJkN2M5ZDYwMGVmMTkucG5n)





字符串（string）是由双引号包围的任意数量Unicode字符的集合，使用反斜线转义。一个字符（character）即一个单独的字符串（character string）。

字符串（string）与C或者Java的字符串非常相似。

![string](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy81NjU2Njc0LTgyOGMxYzEyOTVlMTU4MWQucG5n)



数值（number）也与C或者Java的数值非常相似。除去未曾使用的八进制与十六进制格式。除去一些编码细节。

![number](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy81NjU2Njc0LTJjNzZlZjMyZWY4N2UwYzgucG5n)



空白可以加入到任何符号之间。



这里举例一个比较复杂的json

```json
{
    "name":"网站",
    "money":3.22,
    "status":0,
    "valid":true,
    "address":null,
    "sites": [
        { "name":"Google", "info":[ "Android", "Google 搜索", "Google 翻译" ] },
        { "name":"Runoob", "info":[ "菜鸟教程", "菜鸟工具", "菜鸟微信" ] },
        { "name":"Taobao", "info":[ "淘宝", "网购" ] }
    ]
}
```



具体参见JSON官网http://www.json.org/json-zh.html

很多语言都提供了JSON的解析库，可以参考我的这个博客[Java几种常用JSON库性能比较](https://blog.csdn.net/ThinkWon/article/details/94354358)