# Java转义字符

## 定义

- 转义字符是一种特殊的字符常量。转义字符以反斜线"\"开头，后跟一个或几个字符。转义字符具有特定的含义，不同于字符原有的意义，故称“转义”字符。

## 常见转义字符表

![常见转义字符表](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E5%B8%B8%E8%A7%81%E8%BD%AC%E4%B9%89%E5%AD%97%E7%AC%A6%E8%A1%A8.png)

## Java中需要转义的字符

- 在Java中，不管是String.split()，还是正则表达式，有一些特殊字符需要转义，这些字符是
  (    [     {    /    ^    -    $     ¦    }    ]    )    ?    *    +    . 
  转义方法为字符前面加上"\\"，这样在split、replaceAll时就不会报错。不过要注意，String.contains()方法不需要转义。