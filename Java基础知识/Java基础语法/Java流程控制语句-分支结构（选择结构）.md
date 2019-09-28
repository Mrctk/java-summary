# Java分支结构（选择结构）

[TOC]

## 定义

条件语句可根据不同的条件执行不同的语句。包括if条件语句与switch多分支语句。



## 分类

### if分支结构

#### 第一种格式

##### 格式

if(条件表达式){
  执行的代码块；    
}

##### 执行流程

执行流程图

![if语句格式1](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/if%E8%AF%AD%E5%8F%A5%E6%A0%BC%E5%BC%8F1.jpg)

执行流程说明

首先判断关系表达式看其结果是true还是false
如果是true就执行语句体
如果是false就不执行语句体

##### 举例

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



#### 第二种格式

##### 格式

if(条件表达式){
  执行的代码块; 
}else{
  执行的代码块； 
}

##### 执行流程

执行流程图

![if语句格式2](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/if%E8%AF%AD%E5%8F%A5%E6%A0%BC%E5%BC%8F2.jpg)

执行流程说明

首先判断关系表达式看其结果是true还是false
如果是true就执行语句体1
如果是false就执行语句体2

##### 举例

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



#### 第三种格式

##### 格式

if(条件表达式){
 执行的代码块;
}else if(条件表达式){
 执行的代码块;
}......(此处省略N个else if)
else{
 执行代码块;  
}

##### 执行流程

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

##### 举例

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

#### 注意事项

1、一旦满足某个条件表达式，则进入其执行语句块执行，执行完毕后不会执行其一下的条件语句。
2、如果多个条件表达式之间为“互斥”关系，多个语句之间可以上下调换顺序，一旦是包含关系，要求条件表达式范围小的写到范围大的上边；



### switch分支结构

#### 执行流程

执行流程说明

首先计算出表达式的值
其次，和case依次比较，一旦有对应的值，就会执行相应的语句，在执行的过程中，遇到break就会结束。
最后，如果所有的case都和表达式的值不匹配，就会执行default语句体部分，然后程序结束掉。

执行流程图

![switch执行流程](https://raw.githubusercontent.com/JourWon/image/master/Java%E5%9F%BA%E7%A1%80%E8%AF%AD%E6%B3%95/switch%E8%AF%AD%E5%8F%A5.jpg)

#### 举例

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

#### 注意事项

1、swich（表达式）中表达式的返回值必须是以下几种类型之一：
　　byte，short，char，int，枚举(jdk1.5)，String(jdk1.7)

2、case子句中的值必须是常量，且所有case子句中的值应是不同的；

3、default子句是可任选的，当没有匹配的case时，执行default；

4、break语句用来在执行完一个case分支后使程序跳出swich语句块；如果没有break程序会顺序执行到swich结尾；

if分支结构和switch分支结构区别

if和swich语句很想，如果判断的具体数值不多，而且复合byte、short、int、char这四种类型。建议使用swich语句，因为效率稍高；

其他情况：对区间进行判断，对结果为boolean类型进行判断，使用if，if的使用范围比较广泛。

