# Java流程控制语句-循环结构

[toc]



## 定义

循环语句就是在满足一定条件的情况下反复执行某一个操作。包括while循环语句、do···while循环语句和for循环语句。

## 分类

### for循环语句

#### 执行流程

执行流程图

![for循环语句的执行流程图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvZm9yJUU1JUJFJUFBJUU3JThFJUFGJUU4JUFGJUFEJUU1JThGJUE1JUU3JTlBJTg0JUU2JTg5JUE3JUU4JUExJThDJUU2JUI1JTgxJUU3JUE4JThCJUU1JTlCJUJFLmpwZw)

执行流程说明

A:执行初始化语句
B:执行判断条件语句，看其结果是true还是false
如果是false，循环结束。
如果是true，继续执行。
C:执行循环体语句
D:执行控制条件语句
E:回到B继续

#### 格式

for(初始化语句;判断条件语句;控制条件语句) {
         循环体语句;
}

#### 举例

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



### foreach循环语句

#### 执行流程

执行流程说明

和for循环执行流程相似

#### 格式

for (声明语句 : 表达式) {
 　 //代码句子
}

#### 举例

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

#### 定义

它是Java5后新增的for语句的特殊简化版本，并不能完全替代for语句，但所有foreach语句都可以改写为for语句。foreach语句在遍历数组等时为程序员提供了很大的方便。



### while循环语句

#### 格式

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

#### 执行流程

执行流程图

![while循环语句的执行流程图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvd2hpbGUlRTUlQkUlQUElRTclOEUlQUYlRTglQUYlQUQlRTUlOEYlQTUlRTclOUElODQlRTYlODklQTclRTglQTElOEMlRTYlQjUlODElRTclQTglOEIlRTUlOUIlQkUuanBn)

执行流程说明

while循环语句的循环方式为利用一个条件来控制是否要继续反复执行这个语句。

#### 举例

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

### do…while循环语句

#### 格式

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

#### 执行流程

执行流程图

![do...while循环语句的执行流程图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlOUYlQkElRTclQTElODAlRTglQUYlQUQlRTYlQjMlOTUvZG8uLi53aGlsZSVFNSVCRSVBQSVFNyU4RSVBRiVFOCVBRiVBRCVFNSU4RiVBNSVFNyU5QSU4NCVFNiU4OSVBNyVFOCVBMSU4QyVFNiVCNSU4MSVFNyVBOCU4QiVFNSU5QiVCRS5qcGc)

执行流程说明

A:执行初始化语句;
B:执行循环体语句;
C:执行控制条件语句;
D:执行判断条件语句，看是true还是false
			如果是true，回到B继续
  			如果是false，就结束

#### 举例

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

### 三种循环的区别

虽然可以完成同样的功能，但是还是有小区别：
do…while循环至少会执行一次循环体。
for循环和while循环只有在条件成立的时候才会去执行循环体
for循环语句和while循环语句的小区别：
使用区别：控制条件语句所控制的那个变量，在for循环结束后，就不能再被访问到了，而while循环结束还可以继续使用，如果你想继续使用，就用while，否则推荐使用for。原因是for循环结束，该变量就从内存中消失，能够提高内存的使用效率。

### 跳转语句(控制循环结构)

#### 定义

Java语言中提供了3种跳转语句，分别是break语句、continue语句和return语句。

#### 分类

##### break

break的使用场景：
	在选择结构switch语句中

​	在循环语句中

break的作用：跳出单层循环

注意：离开使用场景是没有意义的。

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

##### continue

continue的使用场景：在循环语句中

continue的作用：结束一次循环，继续下一次的循环

注意：离开使用场景的存在是没有意义的

continue与break区别：
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

##### return

return的使用场景：
	在循环语句中
	在方法中



return的作用：

​	可以从一个方法返回，并把控制权交给调用它的语句。
​	直接结束整个方法，从而结束循环。

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

