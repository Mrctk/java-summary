# Java异常架构与异常关键字

[TOC]



## Java异常简介

Java异常是Java提供的一种识别及响应错误的一致性机制。
Java异常机制可以使程序中异常处理代码和正常业务代码分离，保证程序代码更加优雅，并提高程序健壮性。在有效使用异常的情况下，异常能清晰的回答what, where, why这3个问题：异常类型回答了“什么”被抛出，异常堆栈跟踪回答了“在哪”抛出，异常信息回答了“为什么”会抛出。



## Java异常关键字

• **try**        -- 用于监听。将要被监听的代码(可能抛出异常的代码)放在try语句块之内，当try语句块内发生异常时，异常就被抛出。
• **catch**   -- 用于捕获异常。catch用来捕获try语句块中发生的异常。
• **finally**  -- finally语句块总是会被执行。它主要用于回收在try块里打开的物力资源(如数据库连接、网络连接和磁盘文件)。只有finally块，执行完成之后，才会回来执行try或者catch块中的return或者throw语句，如果finally中使用了return或者throw等终止方法的语句，则就不会跳回执行，直接停止。
• **throw**   -- 用于抛出异常。
• **throws** -- 用在方法签名中，用于声明该方法可能抛出的异常。



下面通过几个示例对这几个关键字进行简单了解。

**示例一: 了解try和catch基本用法**

```java
public class Demo1 {
    public static void main(String[] args) {
        try {
            int i = 10/0;
            System.out.println("i="+i); 
        } catch (ArithmeticException e) {
              System.out.println("Caught Exception"); 
            System.out.println("e.getMessage(): " + e.getMessage()); 
            System.out.println("e.toString(): " + e.toString()); 
            System.out.println("e.printStackTrace():");
            e.printStackTrace(); 
        }
    }
}
```

**运行结果**：

```java
Caught Exception
e.getMessage(): / by zero
e.toString(): java.lang.ArithmeticException: / by zero
e.printStackTrace():
java.lang.ArithmeticException: / by zero
    at Demo1.main(Demo1.java:6)
```

**结果说明**：在try语句块中有除数为0的操作，该操作会抛出java.lang.ArithmeticException异常。通过catch，对该异常进行捕获。
观察结果我们发现，并没有执行System.out.println("i="+i)。这说明try语句块发生异常之后，try语句块中的剩余内容就不会再被执行了。



**示例二: 了解finally的基本用法**

在"示例一"的基础上，我们添加finally语句。

```java
public class Demo2 {
    public static void main(String[] args) {
        try {
            int i = 10/0;
              System.out.println("i="+i); 
        } catch (ArithmeticException e) {
              System.out.println("Caught Exception"); 
            System.out.println("e.getMessage(): " + e.getMessage()); 
            System.out.println("e.toString(): " + e.toString()); 
            System.out.println("e.printStackTrace():");
            e.printStackTrace(); 
        } finally {
            System.out.println("run finally");
        }
    }
}
```

**运行结果**：

```java
Caught Exception
e.getMessage(): / by zero
e.toString(): java.lang.ArithmeticException: / by zero
e.printStackTrace():
java.lang.ArithmeticException: / by zero
    at Demo2.main(Demo2.java:6)
run finally
```

**结果说明**：最终执行了finally语句块。



**示例三: 了解throws和throw的基本用法**

throws是用于在方法声明抛出的异常，而throw是用于抛出异常。

```java
class MyException extends Exception {
    public MyException() {}
    public MyException(String msg) {
        super(msg);
    }
}

public class Demo3 {

    public static void main(String[] args) {
        try {
            test();
        } catch (MyException e) {
            System.out.println("Catch My Exception");
            e.printStackTrace();
        }
    }
    public static void test() throws MyException{
        try {
            int i = 10/0;
              System.out.println("i="+i); 
        } catch (ArithmeticException e) {
            throw new MyException("This is MyException"); 
        }
    }
}
```

**运行结果**：

```java
Catch My Exception
MyException: This is MyException
    at Demo3.test(Demo3.java:24)
    at Demo3.main(Demo3.java:13)
```

**结果说明**：
　　MyException是继承于Exception的子类。test()的try语句块中产生ArithmeticException异常(除数为0)，并在catch中捕获该异常，接着抛出MyException异常。main()方法对test()中抛出的MyException进行捕获处理。



## Java异常架构

![Java异常架构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlQkMlODIlRTUlQjglQjglRTclQUUlODAlRTQlQkIlOEIlRTUlOEYlOEElRTUlODUlQjYlRTYlOUUlQjYlRTYlOUUlODQvSmF2YSVFNSVCQyU4MiVFNSVCOCVCOCVFNiU5RSVCNiVFNiU5RSU4NC5qcGc)

### 1. Throwable

```
    Throwable 是 Java 语言中所有错误与异常的超类。

    Throwable 包含两个子类: Error（错误）和 Exception（异常）它们通常用于指示发生了异常情况。

    Throwable 包含了其线程创建时线程执行堆栈的快照，它提供了 printStackTrace() 等接口用于获取堆栈跟踪数据等信息。
```



### 2. Error（错误）

**定义**：Error 类及其子类。程序中无法处理的错误，表示运行应用程序中出现了严重的错误。

**特点**：此类错误一般表示代码运行时 JVM 出现问题。通常有 Virtual MachineError（虚拟机运行错误）、NoClassDefFoundError（类定义错误）等。比如 OutOfMemoryError：内存不足错误；StackOverflowError：栈溢出错误。此类错误发生时，JVM 将终止线程。

这些错误是不受检异常，非代码性错误。因此，当此类错误发生时，应用程序不应该去处理此类错误。按照Java惯例，我们是不应该是实现任何新的Error子类的！



### 3. Exception（异常）

程序本身可以捕获并且可以处理的异常。Exception 这种异常又分为两类：运行时异常和编译时异常。

#### 3.1 运行时异常

**定义**：RuntimeException 类极其子类，表示 JVM 在运行期间可能出现的错误。

**特点**：Java 编译器不会检查它。也就是说，当程序中可能出现这类异常时，倘若既"没有通过throws声明抛出它"，也"没有用try-catch语句捕获它"，还是会编译通过。比如NullPointerException空指针异常、ArrayIndexOutBoundException数组下标越界异常、ClassCastException类型转换异常、ArithmeticExecption算术异常。此类异常属于不受检异常，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理。虽然 Java 编译器不会检查运行时异常，但是我们也可以通过 throws 进行声明抛出，也可以通过 try-catch 对它进行捕获处理。如果产生运行时异常，则需要通过修改代码来进行避免。例如，若会发生除数为零的情况，则需要通过代码避免该情况的发生！

RuntimeException 异常会由 Java 虚拟机自动抛出并自动捕获（**就算我们没写异常捕获语句运行时也会抛出错误**！！），此类异常的出现绝大数情况是代码本身有问题应该从逻辑上去解决并改进代码。

#### 3.2 编译时异常

**定义**: Exception 中除 RuntimeException 极其子类之外的异常。

**特点**: Java 编译器会检查它。如果程序中出现此类异常，比如 ClassNotFoundException（没有找到指定的类异常），IOException（IO流异常），要么通过throws进行声明抛出，要么通过try-catch进行捕获处理，否则不能通过编译。在程序中，通常不会自定义该类异常，而是直接使用系统提供的异常类。**该异常我们必须手动在代码里添加捕获语句来处理该异常**。



### 4. 受检异常与非受检异常

Java 的所有异常可以分为受检异常（checked exception）和非受检异常（unchecked exception）。

#### 4.1 受检异常

编译器要求必须处理的异常。正确的程序在运行过程中，经常容易出现的、符合预期的异常情况。一旦发生此类异常，就必须采用某种方式进行处理。**除 RuntimeException 及其子类外，其他的 Exception 异常都属于受检异常**。编译器会检查此类异常，也就是说当编译器检查到应用中的某处可能会此类异常时，将会提示你处理本异常——要么使用try-catch捕获，要么使用方法签名中用 throws 关键字抛出，否则编译不通过。

#### 4.2 非受检异常

编译器不会进行检查并且不要求必须处理的异常，也就说当程序中出现此类异常时，即使我们没有try-catch捕获它，也没有使用throws抛出该异常，编译也会正常通过。**该类异常包括运行时异常（RuntimeException极其子类）和错误（Error）。**