# Java转义字符

## 定义

- 转义字符是一种特殊的字符常量。转义字符以反斜线"\"开头，后跟一个或几个字符。转义字符具有特定的含义，不同于字符原有的意义，故称“转义”字符。

## 常见转义字符表

![常见转义字符表](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU1JUI4JUI4JUU4JUE3JTgxJUU4JUJEJUFDJUU0JUI5JTg5JUU1JUFEJTk3JUU3JUFDJUE2JUU4JUExJUE4LnBuZw)

## Java中需要转义的字符

- 在Java中，不管是String.split()，还是正则表达式，有一些特殊字符需要转义，这些字符是
  (    [     {    /    ^    -    $     ¦    }    ]    )    ?    *    +    . 
  转义方法为字符前面加上"\\"，这样在split、replaceAll时就不会报错。不过要注意，String.contains()方法不需要转义。