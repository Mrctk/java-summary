# Java运算符

[toc]



## 定义

运算符指明对操作数的运算方式。



## 分类

### 算术运算符

![算术运算符](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU3JUFFJTk3JUU2JTlDJUFGJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2LnBuZw)

`+ - + - * / % ++ -- +`

  - 注意事项

    - 1、/ 左右两端的类型需要一致；
    - 2、%最后的符号和被模数相同；
    - 3、前++；先+1，后运算 后++；先运算，后+1；
    - 4、+：当String字符串与其他数据类型只能做连接运算；并且结果为String类型；

    

  ### 比较运算符（关系运算符）

`= += -= *= /= %=`

  - 比较运算符1
![比较运算符](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU2JUFGJTk0JUU4JUJFJTgzJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2LnBuZw)


  - 比较运算符2
![比较运算符2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU2JUFGJTk0JUU4JUJFJTgzJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2Mi5wbmc)

  - 注意事项
    - 1、比较运算符的两端都是boolean类型，也就是说要么是true，要么是false；
    - 2、比较运算符的"=="与"="的作用是不同的，使用的时候需要小心。



  ### 赋值运算符

`= += -= *= /= %=`

  - 基本的赋值运算符：=

  - 扩展的赋值运算符：+=,-=,*=,/=,%=
    +=:	a+=20;相当于a = (a的数据类型)(a + 20);

    

  ### 逻辑运算符（符号的两端都是boolean类型）

 `& | ^ ! && || `
  - 注意事项
    - 1、& 与 &&以及|与||的区别：
    - &：左边无论真假，右边都会进行运算；
    - &&：如果左边为假，则右边不进行运算；
    - | 与 || 的区别同上；在使用的时候建议使用&&和||；
    - 2、（^）与或（|）的不同之处是：当左右都为true时，结果为false。

  - 逻辑运算符
![逻辑运算符](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU5JTgwJUJCJUU4JUJFJTkxJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2LnBuZw)

  

  ### 位运算符（两端都是数值型的数据）

  - 位运算符1
![位运算符1](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU0JUJEJThEJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2MS5wbmc)


  - 位运算符2
 ![位运算符2](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU0JUJEJThEJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2Mi5wbmc)

  

  ### 三元运算符（三目运算符）

  - 三元运算符

![三元运算符](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU0JUI4JTg5JUU1JTg1JTgzJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2LnBuZw)

  - 注意事项

    - 1、表达式1与表达式2的类型必须一致；

    - 2、使用三元运算符的地方一定可以使用if..else代替，反之不一定成立；

      

## 运算符的优先级

![运算符的优先级](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvJUU4JUJGJTkwJUU3JUFFJTk3JUU3JUFDJUE2JUU3JTlBJTg0JUU0JUJDJTk4JUU1JTg1JTg4JUU3JUJBJUE3LnBuZw)

- 优先级按照从高到低的顺序书写，也就是优先级为1的优先级最高，优先级14的优先级最低。使用优先级为 1 的小括号可以改变其他运算符的优先级。