# Java基础语法

## 标识符

### 定义

- 给包,类,方法,变量起名字的符号。

### 组成规则

- 标识符由字母、数字、下划线、美元符号组成。

### 命名原则:见名知意

- 包名：全部小写,多级包用.隔开。
举例：com.jourwon
- 类、接口：一个单词首字母大写，多个单词每个单词的首字母大写。
举例：Student,Car,HelloWorld
- 方法和变量：一个单词首字母小写，多个单词从第二个单词开始每个单词的首字母大写。
举例：age,maxAge,show(),getAge()
- 常量：如果是一个单词,所有字母大写，如果是多个单词,所有的单词大写,用下划线区分每个单词。
举例：DATE,MAX_AGE
- 项目名：全部用小写字母，多个单词之间用横杆-分割。
举例：demo，spring-boot

### 注意事项

- 不能以数字开头
- 不能是Java中的关键字
- Java标识符大小写敏感，长度无限制
- 标识符不能包含空格



## 关键字

### 定义

- 被Java语言赋予了特殊含义，用作专门用途的字符串（单词），这些关键字不能用于常量、变量、和任何标识符的名称。

### Java关键字（Java 8版本）

![Java关键字(Java 8版本)](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/Java%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%88Java%208%E7%89%88%E6%9C%AC%EF%BC%89.png)

### Java关键字（Java 8 以后版本）

![Java关键字(Java 8 以后版本)](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/Java%E5%85%B3%E9%94%AE%E5%AD%97%EF%BC%88Java%208%20%E4%BB%A5%E5%90%8E%E7%89%88%E6%9C%AC%EF%BC%89.png)

### 注意事项

- true，false和，null看起来像关键字，但它们实际上是文字; 您不能在程序中将它们用作标识符。



## 注释

### 定义

- 用于解释说明程序的文字

### 分类

- 单行注释
格式： // 注释文字
- 多行注释
格式： /*  注释文字  */
- 文档注释
格式：/** 注释文字 */

### 作用

- 在程序中，尤其是复杂的程序中，适当地加入注释可以增加程序的可读性，有利于程序的修改、调试和交流。注释的内容在程序编译的时候会被忽视，不会产生目标代码，注释的部分不会对程序的执行结果产生任何影响。

### 注意事项

- 多行和文档注释都不能嵌套使用。



## 访问修饰符

### 定义

- Java中，可以使用访问修饰符来保护对类、变量、方法和构造方法的访问。Java 支持 4 种不同的访问权限。

### 分类

- private : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
- default (即缺省，什么也不写，不使用任何关键字）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
- protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）。
- public : 对所有类可见。使用对象：类、接口、变量、方法

### 访问修饰符图

![访问修饰符图](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/Java%E8%AE%BF%E9%97%AE%E4%BF%AE%E9%A5%B0%E7%AC%A6.png)



## 分隔符

### 定义

- 空格、逗号、分号以及行结束符都被称为分隔符，规定任意两个相邻标识符、数字、保留字或语句之间必须至少有一个分隔符，以便程序编译时能够识别。

### 分类

- ; 分号 用来终止一个语句
- {} 花括号、大括号  用来包括自动初始化的数组的值，也用来定义程序块、类、方法以及局部范围
- [] 方括号、中括号  用来声明数组的类型，也用来表示撤消对数组值的引用
- , 逗号  在变量声明中，区分变量说明的各个变量。在for控制语句中，用来将圆括号内的语句连接起来
- . 原点  用来将软件包的名字与它的子包或类分隔。也用来将引用变量与变量或方法分隔
- ()  圆括号  在定义和调用方法时用来容纳参数表。在控制语句或强制类型转换组成的表达式中用来表示执行或计算的优先权

### 注意事项

- 必须都是半角下的英文符号。



## 转义字符

### 定义

- 转义字符是一种特殊的字符常量。转义字符以反斜线"\"开头，后跟一个或几个字符。转义字符具有特定的含义，不同于字符原有的意义，故称“转义”字符。

### 常见转义字符表

![常见转义字符表](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E5%B8%B8%E8%A7%81%E8%BD%AC%E4%B9%89%E5%AD%97%E7%AC%A6%E8%A1%A8.png)

### Java中需要转义的字符

- 在Java中，不管是String.split()，还是正则表达式，有一些特殊字符需要转义，这些字符是
  (    [     {    /    ^    -    $     ¦    }    ]    )    ?    *    +    . 
  转义方法为字符前面加上"\\"，这样在split、replaceAll时就不会报错。不过要注意，String.contains()方法不需要转义。



## 空行

### 空白行，或者有注释的行，Java编译器都会忽略掉



## 进制

### 定义

- 进制也就是进位计数制，是人为定义的带进位的计数方法。
 十进制是逢十进一，十六进制是逢十六进一，二进制就是逢二进一，以此类推，x进制就是逢x进位。

### 常用进制类型

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

	- 0x****；(以0x或者0X开始，*表示0~9或者a~f组成 比如：0x12c）

### 进制转换

- 十进制转二进制
  方法为：十进制数除2取余法，即十进制数除2，余数为权位上的数，得到的商值继续除2，依此步骤继续向下运算直到商为0为止。

  - 150十进制转二进制
  - ![150十进制转二进制](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/150%E5%8D%81%E8%BF%9B%E5%88%B6%E8%BD%AC%E4%BA%8C%E8%BF%9B%E5%88%B6.png)

- 二进制转十进制
  方法为：把二进制数按权展开、相加即得十进制数。
  - 150二进制转十进制
  - ![150二进制转十进制](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/150%E4%BA%8C%E8%BF%9B%E5%88%B6%E8%BD%AC%E5%8D%81%E8%BF%9B%E5%88%B6.png)



## 流程控制语句

### 定义

流程是指程序运行时，各语句的执行顺序。流程控制语句就是用来控制程序中各语句执行的顺序。

### 分类

#### 顺序结构

##### 定义

顺序结构是程序中最简单最基本的流程控制，没有特定的语法结构，按照代码的先后顺序，依次执行，程序中大多数的代码都是这样执行的

##### 举例
```java
/*
 * 顺序结构：从上往下，依次执行
 */
public class OrderDemo {
	public static void main(String[] args) {
		System.out.println("开始");
		System.out.println("语句A");
		System.out.println("语句B");
		System.out.println("语句C");
		System.out.println("结束");
	}
}
```


#### 分支结构（选择结构）

##### 定义

条件语句可根据不同的条件执行不同的语句。包括if条件语句与switch多分支语句。

##### 分类

##### if分支结构

第一种格式

###### 格式

if(条件表达式){
  执行的代码块；    
}

###### 执行流程

执行流程图

![if语句格式1](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/if%E8%AF%AD%E5%8F%A5%E6%A0%BC%E5%BC%8F1.jpg)

执行流程说明

首先判断关系表达式看其结果是true还是false
如果是true就执行语句体
如果是false就不执行语句体

###### 举例
```java
public class IfDemo {
	public static void main(String[] args) {
		System.out.println("开始");
		// 定义两个变量
		int a = 10;
		int b = 20;

		if (a == b) {
			System.out.println("a等于b");
		}
	
		int c = 10;
		if (a == c) {
			System.out.println("a等于c");
		}
	
		System.out.println("结束");
	}
}
```
##### 第二种格式

###### 格式

if(条件表达式){
  执行的代码块; 
}else{
  执行的代码块； 
}

###### 执行流程

执行流程图

![if语句格式2](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/if%E8%AF%AD%E5%8F%A5%E6%A0%BC%E5%BC%8F2.jpg)

执行流程说明

首先判断关系表达式看其结果是true还是false
如果是true就执行语句体1
如果是false就执行语句体2

###### 举例
```java
public class IfDemo2 {
	public static void main(String[] args) {
		System.out.println("开始");
		// 判断给定的数据是奇数还是偶数
		// 定义变量
	int a = 100;
		// 给a重新赋值
	a = 99;

	if (a % 2 == 0) {
		System.out.println("a是偶数");
	} else {
		System.out.println("a是奇数");
	}
	
		System.out.println("结束");
	}
}
```
##### 第三种格式

###### 格式

if(条件表达式){
 执行的代码块;
}else if(条件表达式){
 执行的代码块;
}......(此处省略N个else if)
else{
 执行代码块;  
}

###### 执行流程

执行流程图

![if语句格式3](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/if%E8%AF%AD%E5%8F%A5%E6%A0%BC%E5%BC%8F3.jpg)

执行流程说明

首先判断关系表达式1看其结果是true还是false
如果是true就执行语句体1
如果是false就继续判断关系表达式2看其结果是true还是false
如果是true就执行语句体2
如果是false就继续判断关系表达式…看其结果是true还是false
…
如果没有任何关系表达式为true，就执行语句体n+1

###### 举例

```java
public class IfDemo3 {
	public static void main(String[] args) {
		// x和y的关系满足如下：
		// x>=3 y = 2x + 1;
		// -1<=x<3 y = 2x;
		// x<=-1 y = 2x – 1;
		// 根据给定的x的值，计算出y的值并输出。

		// 定义变量
		int x = 5;
		
		/*
		int y;
		if (x >= 3) {
			y = 2 * x + 1;
		} else if (x >= -1 && x < 3) {
			y = 2 * x;
		} else if (x <= -1) {
			y = 2 * x - 1;
		}else {
			y = 0;
		}
		*/
		
		int y = 0;
		if (x >= 3) {
			y = 2 * x + 1;
		} else if (x >= -1 && x < 3) {
			y = 2 * x;
		} else if (x <= -1) {
			y = 2 * x - 1;
		}
		
		System.out.println("y的值是："+y);
	}
}
```

###### 注意事项

1、一旦满足某个条件表达式，则进入其执行语句块执行，执行完毕后不会执行其一下的条件语句。
2、如果多个条件表达式之间为“互斥”关系，多个语句之间可以上下调换顺序，一旦是包含关系，要求条件表达式范围小的写到范围大的上边；



##### switch分支结构

###### 执行流程

执行流程说明

首先计算出表达式的值
其次，和case依次比较，一旦有对应的值，就会执行相应的语句，在执行的过程中，遇到break就会结束。
最后，如果所有的case都和表达式的值不匹配，就会执行default语句体部分，然后程序结束掉。

执行流程图

![switch执行流程](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/switch%E8%AF%AD%E5%8F%A5.jpg)

###### 举例
```java
public class SwitchDemo {
	public static void main(String[] args) {
		//创建键盘录入对象
		Scanner sc = new Scanner(System.in);
		
		//接收数据
		System.out.println("请输入一个数字(1-7)：");
		int weekday = sc.nextInt();
		
		//switch语句实现选择
		switch(weekday) {
		case 1:
			System.out.println("星期一");
			break;
		case 2:
			System.out.println("星期二");
			break;
		case 3:
			System.out.println("星期三");
			break;
		case 4:
			System.out.println("星期四");
			break;
		case 5:
			System.out.println("星期五");
			break;
		case 6:
			System.out.println("星期六");
			break;
		case 7:
			System.out.println("星期日");
			break;
		default:
			System.out.println("你输入的数字有误");
			break;
		}
	}
}
```
###### 注意事项

1、swich（表达式）中表达式的返回值必须是以下几种类型之一：
　　byte，short，char，int，枚举(jdk1.5)，String(jdk1.7)

2、case子句中的值必须是常量，且所有case子句中的值应是不同的；

3、default子句是可任选的，当没有匹配的case时，执行default；

4、break语句用来在执行完一个case分支后使程序跳出swich语句块；如果没有break程序会顺序执行到swich结尾；

if分支结构和switch分支结构区别

if和swich语句很想，如果判断的具体数值不多，而且复合byte、short、int、char这四种类型。建议使用swich语句，因为效率稍高；

其他情况：对区间进行判断，对结果为boolean类型进行判断，使用if，if的使用范围比较广泛。



#### 循环结构

##### 定义

循环语句就是在满足一定条件的情况下反复执行某一个操作。包括while循环语句、do···while循环语句和for循环语句。

##### 分类

##### for循环语句

###### 执行流程

执行流程图

![for循环语句的执行流程图](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/for%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

执行流程说明

A:执行初始化语句
B:执行判断条件语句，看其结果是true还是false
如果是false，循环结束。
如果是true，继续执行。
C:执行循环体语句
D:执行控制条件语句
E:回到B继续

###### 格式

for(初始化语句;判断条件语句;控制条件语句) {
         循环体语句;
}

###### 举例

```java
public class ForDemo {
	public static void main(String[] args) {
		//原始写法
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("HelloWorld");
		System.out.println("-------------------------");
		

		//用循环改进
		for(int x=1; x<=10; x++) {
			System.out.println("HelloWorld");
		}
	}
}
```



##### foreach循环语句

###### 执行流程

执行流程说明

和for循环执行流程相似

###### 格式

for (声明语句 : 表达式) {
 　 //代码句子
}

###### 举例
```java
public class Test {
    public static void main(String args[]) {
        int [] numbers = {10, 20, 30, 40, 50};
        
        for ( int x : numbers ) {
            System.out.print( x );
            System.out.print(",");
        }
        
        System.out.print();
        String [] names = {"James", "Larry", "Tom", "Lacy"};
        
        for ( String name : names ) {
            System.out.print( name );
            System.out.print(",");
        }
    }
}
```
###### 定义

它是Java5后新增的for语句的特殊简化版本，并不能完全替代for语句，但所有foreach语句都可以改写为for语句。foreach语句在遍历数组等时为程序员提供了很大的方便。



##### while循环语句

###### 格式

基本格式
   while(判断条件语句) {
         循环体语句;
   }
扩展格式
   初始化语句;
   while(判断条件语句) {
         循环体语句;
         控制条件语句;
}

###### 执行流程

执行流程图

![while循环语句的执行流程图](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/while%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

执行流程说明

while循环语句的循环方式为利用一个条件来控制是否要继续反复执行这个语句。

###### 举例
```java
public class WhileDemo {
	public static void main(String[] args) {
		//输出10次HelloWorld
		/*
		for(int x=1; x<=10; x++) {
			System.out.println("HellloWorld");
		}
		*/
		
		//while循环实现
		int x=1;
		while(x<=10) {
			System.out.println("HellloWorld");
			x++;
		}
	}
}
```


##### do…while循环语句

###### 格式

基本格式
   do {
         循环体语句;
   }while((判断条件语句);
扩展格式
   初始化语句;
   do {
         循环体语句;
         控制条件语句;
} while((判断条件语句);

###### 执行流程

执行流程图

![do...while循环语句的执行流程图](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/do...while%E5%BE%AA%E7%8E%AF%E8%AF%AD%E5%8F%A5%E7%9A%84%E6%89%A7%E8%A1%8C%E6%B5%81%E7%A8%8B%E5%9B%BE.jpg)

执行流程说明

A:执行初始化语句;
B:执行循环体语句;
C:执行控制条件语句;
D:执行判断条件语句，看是true还是false
			如果是true，回到B继续
  			如果是false，就结束

###### 举例
```java
public class DoWhileDemo {
	public static void main(String[] args) {
		//输出10次 HelloWorld
		/*
		for(int x=1; x<=10; x++) {
			System.out.println("HelloWorld");
		}
		*/
		

		//do...while改写
		int x=1;
		do {
			System.out.println("HelloWorld");
			x++;
		}while(x<=10);
	}
}
```


##### 三种循环的区别

虽然可以完成同样的功能，但是还是有小区别：
do…while循环至少会执行一次循环体。
for循环和while循环只有在条件成立的时候才会去执行循环体
for循环语句和while循环语句的小区别：
使用区别：控制条件语句所控制的那个变量，在for循环结束后，就不能再被访问到了，而while循环结束还可以继续使用，如果你想继续使用，就用while，否则推荐使用for。原因是for循环结束，该变量就从内存中消失，能够提高内存的使用效率。

##### 跳转语句(控制循环结构)

###### 定义

Java语言中提供了3种跳转语句，分别是break语句、continue语句和return语句。

分类

###### break

break的使用场景

break的使用场景：
在选择结构switch语句中，在循环语句中
离开使用场景的存在是没有意义的
break的作用：跳出单层循环

举例
```java
/*
 * break:中断的意思
 * 使用场景：
 * 		A:switch语句中
 * 		B:循环中
 * 注意：
 * 		离开使用场景是没有意义的。
 * 作用：
 * 		跳出循环，让循环提前结束
 */
public class BreakDemo {
	public static void main(String[] args) {
		//break;
		
		for(int x=1; x<=10; x++) {
			if(x == 3) {
				break;
			}
			System.out.println("HelloWorld");
		}
	}
}
```
###### continue

continue的使用场景

continue的使用场景：
在循环语句中
离开使用场景的存在是没有意义的
continue的作用：
单层循环对比break，然后总结两个的区别
break  退出当前循环
continue  退出本次循环

举例
```java
/*
 * continue:继续的意思
 * 使用场景：
 * 		循环中
 * 注意：
 * 		离开使用场景是没有意义的
 * 作用：
 * 		结束一次循环，继续下一次的循环
 * 区别：
 * 		break:退出循环
 * 		continue:结束一次循环，继续下一次的循环
 */
public class ContinueDemo {
	public static void main(String[] args) {
		//continue;
		
		for(int x=1; x<=10; x++) {
			if(x == 3) {
				//break;
				continue;
			}
			System.out.println("HelloWorld");
		}
	}
}
```
###### return

return的使用场景

return的使用场景：
在循环语句中
在方法中
return的作用：可以从一个方法返回，并把控制权交给调用它的语句。
直接结束整个方法，从而结束循环。

举例
```java
public class ReturnDemo {
	public static void main(String[] args) {
		getStr();
	}

	public String getStr() {
	    		return "Hello";
		}

}
```


## 表达式

### 定义

- 用运算符把常量或者变量连接起来符号java语法的式子就可以称为表达式。

### 类型和值

- 表达式值的数据类型即为表达式的类型。
- 对表达式中操作数进行运算得到的结果是表达式的值。

### 运算顺序

- 应按照运算符的优先级从高到低的顺序进行;
- 优先级相同的运算符按照事先约定的结合方向进行;

### 举例

- a + b
3.14 + a
(x + y) * z + 100
boolean b= i < 10 && (i%10 != 0)



## 运算符

### 定义

### 分类

- 算术运算符
- ![算术运算符](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E7%AE%97%E6%9C%AF%E8%BF%90%E7%AE%97%E7%AC%A6.png)
  - + - + - * / % ++ -- +
  - 注意事项

    - 1、/ 左右两端的类型需要一致；
    - 2、%最后的符号和被模数相同；
    - 3、前++；先+1，后运算 后++；先运算，后+1；
    - 4、+：当String字符串与其他数据类型只能做连接运算；并且结果为String类型；
- 比较运算符（关系运算符）

  - = += -= *= /= %=
  - 比较运算符1
  - ![比较运算符](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E6%AF%94%E8%BE%83%E8%BF%90%E7%AE%97%E7%AC%A6.png)
  - 比较运算符2
  - ![比较运算符2](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E6%AF%94%E8%BE%83%E8%BF%90%E7%AE%97%E7%AC%A62.png)
  - 注意事项

    - 1、比较运算符的两端都是boolean类型，也就是说要么是true，要么是false；
    - 2、比较运算符的"=="与"="的作用是不同的，使用的时候需要小心。
- 赋值运算符

  - = += -= *= /= %=
  - 基本的赋值运算符：=
  - 扩展的赋值运算符：+=,-=,*=,/=,%=
  	+=:	a+=20;相当于a = (a的数据类型)(a + 20);
- 逻辑运算符（符号的两端都是boolean类型）

  - & | ^ ! && || 
  - 注意事项

    - 1、& 与 &&以及|与||的区别：
    - &：左边无论真假，右边都会进行运算；
    - &&：如果左边为假，则右边不进行运算；
    - | 与 || 的区别同上；在使用的时候建议使用&&和||；
    - 2、（^）与或（|）的不同之处是：当左右都为true时，结果为false。
  - 逻辑运算符
  - ![逻辑运算符](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E9%80%BB%E8%BE%91%E8%BF%90%E7%AE%97%E7%AC%A6.png)
- 位运算符（两端都是数值型的数据）

  - 位运算符1
  - ![位运算符1](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E4%BD%8D%E8%BF%90%E7%AE%97%E7%AC%A61.png)
  - 位运算符2
  - ![位运算符2](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E4%BD%8D%E8%BF%90%E7%AE%97%E7%AC%A62.png)
- 三元运算符（三目运算符）

  - 三元运算符
  - ![三元运算符](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E4%B8%89%E5%85%83%E8%BF%90%E7%AE%97%E7%AC%A6.png)
  - 注意事项

    - 1、表达式1与表达式2的类型必须一致；
    - 2、使用三元运算符的地方一定可以使用if..else代替，反之不一定成立；

### 运算符的优先级

![运算符的优先级](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/%E8%BF%90%E7%AE%97%E7%AC%A6%E7%9A%84%E4%BC%98%E5%85%88%E7%BA%A7.png)

- 优先级按照从高到低的顺序书写，也就是优先级为1的优先级最高，优先级14的优先级最低。使用优先级为 1 的小括号可以改变其他运算符的优先级。



## 变量

### 定义

- 在程序执行的过程中，在某个范围内其值可以发生改变的量。
从本质上讲，变量其实是内存中的一小块区域

### 分类

- 按被声明的位置划分

	- 成员变量

	  方法外部，类内部定义的变量

		- 类变量(静态变量)：独立于方法之外的变量，用 static 修饰。

			- 类变量也称为静态变量，在类中以static关键字声明，但必须在方法构造方法和语句块之外。
			- 无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。
			- 静态变量除了被声明为常量外很少使用。常量是指声明为public/private，final和static类型的变量。常量初始化后不可改变。
			- 静态变量储存在静态存储区。经常被声明为常量，很少单独使用static声明变量。
			- 静态变量在第一次被访问时创建，在程序结束时销毁。
			- 与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为public类型。
			- 默认值和实例变量相似。数值型变量默认值是0，布尔型默认值是false，引用类型默认值是null。变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
			- 静态变量可以通过：ClassName.VariableName的方式访问。
			- 类变量被声明为public static final类型时，类变量名称一般建议使用大写字母。如果静态变量不是public和final类型，其命名方式与实例变量以及局部变量的命名方式一致。

		- 实例变量(非静态变量)：独立于方法之外的变量，不过没有 static 修饰。

			- 实例变量声明在一个类中，但在方法、构造方法和语句块之外；
			- 当一个对象被实例化之后，每个实例变量的值就跟着确定；
			- 实例变量在对象创建的时候创建，在对象被销毁的时候销毁；
			- 实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息；
			- 实例变量可以声明在使用前或者使用后；
			- 访问修饰符可以修饰实例变量；
			- 实例变量对于类中的方法、构造方法或者语句块是可见的。一般情况下应该把实例变量设为私有。通过使用访问修饰符可以使实例变量对子类可见；
			- 实例变量具有默认值。数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。变量的值可以在声明时指定，也可以在构造方法中指定；
			- 实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名：ObejectReference.VariableName。

		- 类变量和实例变量的区别

			- 调用方式

				- 静态变量也称为类变量，可以直接通过类名调用。也可以通过对象名调用。这个变量属于类。
				- 成员变量也称为实例变量，只能通过对象名调用。这个变量属于对象。

			- 存储位置

				- 静态变量存储在方法区长中的静态区。
				- 成员变量存储在堆内存。

			- 生命周期

				- 静态变量随着类的加载而存在，随着类的消失而消失。生命周期长。
				- 成员变量随着对象的创建而存在，随着对象的消失而消失。

			- 与对象的相关性

				- 静态变量是所有对象共享的数据。
				- 成员变量是每个对象所特有的数据。

	- 局部变量

		- 局部变量：类的方法中的变量。
		- 局部变量声明在方法、构造方法或者语句块中；
		- 局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁；
		- 访问修饰符不能用于局部变量；
		- 局部变量只在声明它的方法、构造方法或者语句块中可见；
		- 局部变量是在栈上分配的。
		- 局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。

	- 成员变量和局部变量的区别

		- 作用域

			- 成员变量：针对整个类有效。
			- 局部变量：只在某个范围内有效。(一般指的就是方法,语句体内)

		- 存储位置

			- 成员变量：随着对象的创建而存在，随着对象的消失而消失，存储在堆内存中。
			- 局部变量：在方法被调用，或者语句被执行的时候存在，存储在栈内存中。当方法调用完，或者语句结束后，就自动释放。

		- 生命周期

			- 成员变量：随着对象的创建而存在，随着对象的消失而消失
			- 局部变量：当方法调用完，或者语句结束后，就自动释放。

		- 初始值

			- 成员变量：有默认初始值。
			- 局部变量：没有默认初始值，使用前必须赋值。

	- 使用原则

		- 在使用变量时需要遵循的原则为：就近原则 
首先在局部范围找，有就使用；接着在成员位置找。

- 按所属的数据类型划分

	- 基本数据类型变量

		- 基本数据类型

			- byte,short,int,long,float,double,char,boolean

		- 变量的定义格式

			- 数据类型 变量名 = 初始化值;

		- 注意

			- 整数默认是int类型，定义long类型的数据时，要在数据后面加L。
			- 浮点数默认是double类型，定义float类型的数据时，要在数据后面加F。

	- 引用数据类型变量

### 定义格式

- 数据类型 变量名 = 初始化值;
注意：格式是固定的，记住格式，以不变应万变
- 举例
```java
public class Variable{
    // 类变量
    static int allClicks=0;    
     // 实例变量
    String str="hello world";  
    public void method(){
		 	// 局部变量
        int i =0;  
    }
}
```
### 注意事项

- 类外面不能有变量的声明



## 常量

### 定义

- 常量定义：在程序执行的过程中，其值不可以发生改变的量。常量不同于常量值，它可以在程序中用符号来代替常量值使用，因此在使用前必须先定义。
- 常量值定义：常量和常量值是不同的概念，常量值又称为字面常量，它是通过数据直接表示的。
- 关系：常量值是常量的具体和直观的表现形式，常量是形式化的表现。通常在程序中既可以直接使用常量值，也可以使用常量。

### 分类

- 字符串常量	用双引号括起来的内容(“HelloWorld”)
- 整数常量	所有整数(12,-23)
- 小数常量	所有小数(12.34)
- 字符常量	用单引号括起来的内容(‘a’,’A’,’0’)
- 布尔常量	较为特有，只有true和false
- 空常量		null(数组部分讲解)

### 举例

- Java 语言使用 final 关键字来定义一个常量
final int COUNT=10;
final float HEIGHT=10.2F;

### 注意事项

- 在定义常量时就需要对该常量进行初始化。
- final 关键字不仅可以用来修饰基本数据类型的常量，还可以用来修饰对象的引用或者方法。
- 为了与变量区别，常量取名一般都用大写字符。



## 数据类型

### 定义

- Java语言是强类型语言，对于每一种数据都定义了明确的具体的数据类型，在内存中分配了不同大小的内存空间。

### 分类

- 基本数据类型

	- 数值型

		- 整数类型(byte,short,int,long)
		- 浮点类型(float,double)

	- 字符型(char)
	- 布尔型(boolean)

- 引用数据类型

	- 类(class)
	- 接口(interface)
	- 数组([])

### 计算机存储单元

- 定义：变量是内存中的小容器，用来存储数据。那么计算机内存是怎么存储数据的呢？无论是内存还是硬盘，计算机存储设备的最小信息单元叫“位（bit）”，我们又称之为“比特位”，通常用小写的字母b表示。而计算机最小的存储单元叫“字节（byte）”，通常用大写字母B表示，字节是由连续的8个位组成。
- 常用存储单元关系

	- 1B= 8b
1KB = 1024B
1MB = 1024KB
1GB = 1024MB
1TB = 1024GB

### Java基本数据类型图

![Java基本数据类型](https://raw.githubusercontent.com/JourWon/image/master/Java基础语法/Java基本数据类型.png)

### 数据类型转换

- 定义

	- 数据类型的转换是在所赋值的数值类型和被变量接收的数据类型不一致时发生的，它需要从一种数据类型转换成另一种数据类型。

- 分类

	- 隐式转换

		- 定义

			- 在运算过程中，由于不同的数据类型会转换成同一种数据类型，所以整型、浮点型以及字符型都可以参与混合运算。自动转换的规则是从低级类型数据转换成高级类型数据。

		- 转换规则

			- 数值型数据的转换：byte→short→int→long→float→double。
			- 字符型转换为整型：char→int。

		- 转换条件

			- 自动类型转换的实现需要同时满足两个条件：①两种数据类型彼此兼容，②目标类型的取值范围大于源数据类型（低级类型数据转换成高级类型数据）。例如 byte 类型向 short 类型转换时，由于 short 类型的取值范围较大，会自动将 byte 转换为 short 类型。

	- 显式转换

		- 定义

			- 当两种数据类型不兼容，或目标类型的取值范围小于源类型时，自动转换将无法进行，这时就需要进行强制类型转换。

		- 语法格式

			- 目标类型 变量名 = (目标类型) (被转换的数据);
举例：int b = (byte)(a + b); 

		- 注意

			- 如果超出了被赋值的数据类型的取值范围得到的结果会与你期望的结果不同
			- 不建议强制转换，因为会有精度的损失。
