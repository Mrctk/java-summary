# Java进制

## 定义

- 进制也就是进位计数制，是人为定义的带进位的计数方法。
  十进制是逢十进一，十六进制是逢十六进一，二进制就是逢二进一，以此类推，x进制就是逢x进位。

## 常用进制类型

- 二进制
  - 计算机底层都是使用二进制进行存储以及运算
  - 0b****;(以0b或者0B开始，*表示0或1）比如：010101
  - 原码、反码、补码
    - 原码
      - 原码：将一个整数转换成二进制表示
        以 int 类型为例，int类型占4个字节、共32位。
        例如：
        2 的原码为：00000000 00000000 00000000 00000010
        -2的原码为：10000000 00000000 00000000 00000010
    - 反码
      - 反码
        正数的反码：与原码相同
        负数的反码：原码的符号位不变，其他位取反
        例如，-2 的反码为：11111111 11111111 11111111 11111101
    - 补码
      - 补码
        正数的补码：与原码相同
        负数的补码：反码+1
        例如，-2 的补码为：01111111 11111111 11111111 11111110
    - 注意事项
      - 二进制的最高位是符号位（“0”代表正数，“1”代表负数）；
      - Java中没有无符号数；
      - 计算机以整数的补码进行运算；
- 八进制
  - 0****；(以数字0开始，*表示0到7任意数字 比如：012345） 
- 十进制
  - 0~9组成 比如：100
- 十六进制
  - 0x****；(以0x或者0X开始，*表示0\~9或者a\~f组成 比如：0x12c）

## 进制转换

- 十进制转二进制
  方法为：十进制数除2取余法，即十进制数除2，余数为权位上的数，得到的商值继续除2，依此步骤继续向下运算直到商为0为止。
  - 150十进制转二进制
  - ![150十进制转二进制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvMTUwJUU1JThEJTgxJUU4JUJGJTlCJUU1JTg4JUI2JUU4JUJEJUFDJUU0JUJBJThDJUU4JUJGJTlCJUU1JTg4JUI2LnBuZw)
- 二进制转十进制
  方法为：把二进制数按权展开、相加即得十进制数。
  - 150二进制转十进制
  - ![150二进制转十进制](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvMTUwJUU0JUJBJThDJUU4JUJGJTlCJUU1JTg4JUI2JUU4JUJEJUFDJUU1JThEJTgxJUU4JUJGJTlCJUU1JTg4JUI2LnBuZw)

