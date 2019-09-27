## Java异常简介及其架构

### Java异常简介

Java异常是Java提供的一种识别及响应错误的一致性机制。
Java异常机制可以使程序中异常处理代码和正常业务代码分离，保证程序代码更加优雅，并提高程序健壮性。在有效使用异常的情况下，异常能清晰的回答what, where, why这3个问题：异常类型回答了“什么”被抛出，异常堆栈跟踪回答了“在哪”抛出，异常信息回答了“为什么”会抛出。



####  Java异常关键字
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



### Java异常架构

![Java异常架构](https://raw.githubusercontent.com/JourWon/image/master/Java异常简介及其架构/Java异常架构.jpg)

#### **1. Throwable**

        Throwable 是 Java 语言中所有错误与异常的超类。
    
        Throwable 包含两个子类: Error（错误）和 Exception（异常）它们通常用于指示发生了异常情况。
    
        Throwable 包含了其线程创建时线程执行堆栈的快照，它提供了 printStackTrace() 等接口用于获取堆栈跟踪数据等信息。



#### **2. Error（错误）**

**定义**：Error 类及其子类。程序中无法处理的错误，表示运行应用程序中出现了严重的错误。

**特点**：此类错误一般表示代码运行时 JVM 出现问题。通常有 Virtual MachineError（虚拟机运行错误）、NoClassDefFoundError（类定义错误）等。比如 OutOfMemoryError：内存不足错误；StackOverflowError：栈溢出错误。此类错误发生时，JVM 将终止线程。

这些错误是不受检异常，非代码性错误。因此，当此类错误发生时，应用程序不应该去处理此类错误。按照Java惯例，我们是不应该是实现任何新的Error子类的！




#### **3. Exception（异常）**

程序本身可以捕获并且可以处理的异常。Exception 这种异常又分为两类：运行时异常和编译时异常。

##### 3.1 运行时异常

**定义**：RuntimeException 类极其子类，表示 JVM 在运行期间可能出现的错误。

**特点**：Java 编译器不会检查它。也就是说，当程序中可能出现这类异常时，倘若既"没有通过throws声明抛出它"，也"没有用try-catch语句捕获它"，还是会编译通过。比如NullPointerException空指针异常、ArrayIndexOutBoundException数组下标越界异常、ClassCastException类型转换异常、ArithmeticExecption算术异常。此类异常属于不受检异常，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理。虽然 Java 编译器不会检查运行时异常，但是我们也可以通过 throws 进行声明抛出，也可以通过 try-catch 对它进行捕获处理。如果产生运行时异常，则需要通过修改代码来进行避免。例如，若会发生除数为零的情况，则需要通过代码避免该情况的发生！

RuntimeException 异常会由 Java 虚拟机自动抛出并自动捕获**（就算我们没写异常捕获语句运行时也会抛出错误！！）**，此类异常的出现绝大数情况是代码本身有问题应该从逻辑上去解决并改进代码。

##### 3.2 编译时异常

**定义**: Exception 中除 RuntimeException 极其子类之外的异常。

**特点**: Java 编译器会检查它。如果程序中出现此类异常，比如 ClassNotFoundException（没有找到指定的类异常），IOException（IO流异常），要么通过throws进行声明抛出，要么通过try-catch进行捕获处理，否则不能通过编译。在程序中，通常不会自定义该类异常，而是直接使用系统提供的异常类。**该异常我们必须手动在代码里添加捕获语句来处理该异常**。




#### 4. 受检异常与不受检异常
Java 的所有异常可以分为受检异常（checked exception）和不受检异常（unchecked exception）。

##### 4.1 受检异常

编译器要求必须处理的异常。正确的程序在运行过程中，经常容易出现的、符合预期的异常情况。一旦发生此类异常，就必须采用某种方式进行处理。除 RuntimeException 及其子类外，其他的 Exception 异常都属于受检异常。编译器会检查此类异常，也就是说当编译器检查到应用中的某处可能会此类异常时，将会提示你处理本异常——要么使用try-catch捕获，要么使用方法签名中用 throws 关键字抛出，否则编译不通过。

##### 4.2 不受检异常

编译器不会进行检查并且不要求必须处理的异常，也就说当程序中出现此类异常时，即使我们没有try-catch捕获它，也没有使用throws抛出该异常，编译也会正常通过。该类异常包括运行时异常（RuntimeException极其子类）和错误（Error）。




### 异常处理流程

在Java应用中，异常的处理机制分为抛出异常和捕获异常。

#### 1. **抛出异常**

当一个方法出现错误而引发异常时，该方法会将该异常类型以及异常出现时的程序状态信息封装为异常对象，并交给本应用。运行时，该应用将寻找处理异常的代码并执行。任何代码都可以通过throw关键词抛出异常，比如Java源代码抛出异常、自己编写的代码抛出异常等。



#### 2. **捕获异常**

一旦方法抛出异常，系统自动根据该异常对象寻找合适异常处理器（Exception Handler）来处理该异常。所谓合适类型的异常处理器指的是异常对象类型和异常处理器类型一致。

对于不同的异常，Java采用不同的异常处理方式：

1、运行时异常将由系统自动抛出，应用本身可以选择处理或者忽略该异常。

2、对于方法中产生的 Error，该异常一旦发生 JVM 将自行处理该异常，因此 Java 允许应用不抛出此类异常。

3、对于所有的受检异常，必须进行捕获或者抛出该方法之外交给上层处理。也就是当一个方法存在异常时，要么使用try-catch捕获，要么使用该方法使用throws将该异常抛调用该方法的上层调用者。



#### 3. **异常的抛出与捕获**

##### 3.1 **直接抛出异常**

通常，应该捕获那些知道如何处理的异常，将不知道如何处理的异常继续传递下去。传递异常可以在方法签名处使用 **throws** 关键字声明可能会抛出的异常。

```java
private static void readFile(String filePath) throws IOException {
    File file = new File(filePath);
    String result;
    BufferedReader reader = new BufferedReader(new FileReader(file));
    while((result = reader.readLine())!=null) {
        System.out.println(result);
    }
    reader.close();
}
```
##### 3.2 **封装异常再抛出**

有时我们会从 catch 中抛出一个异常，目的是为了改变异常的类型。多用于在多系统集成时，当某个子系统故障，异常类型可能有多种，可以用统一的异常类型向外暴露，不需暴露太多内部异常细节。

```java
private static void readFile(String filePath) throws MyException {    
    try {
        // code
    } catch (IOException e) {
        MyException ex = new MyException("read file failed.");
        ex.initCause(e);
        throw ex;
    }
}
```
##### 3.3 **捕获异常**

在一个 try-catch 语句块中可以捕获多个异常类型，并对不同类型的异常做出不同的处理

```java
private static void readFile(String filePath) {
    try {
        // code
    } catch (FileNotFoundException e) {
        // handle FileNotFoundException
    } catch (IOException e){
        // handle IOException
    }
}
```

同一个 catch 也可以捕获多种类型异常，用 | 隔开

```java
private static void readFile(String filePath) {
    try {
        // code
    } catch (FileNotFoundException | UnknownHostException e) {
        // handle FileNotFoundException or UnknownHostException
    } catch (IOException e){
        // handle IOException
    }
}
```

##### 3.4 **自定义异常**

习惯上，定义一个异常类应包含两个构造函数，一个无参构造函数和一个带有详细描述信息的构造函数（Throwable 的 toString 方法会打印这些详细信息，调试时很有用）

```java
public class MyException extends Exception {
    public MyException(){ }
    public MyException(String msg){
        super(msg);
    }
    // ...
}
```

##### 3.5 **try-catch-finally**

当方法中发生异常，异常处之后的代码不会再执行，如果之前获取了一些本地资源需要释放，则需要在方法正常结束时和 catch 语句中都调用释放本地资源的代码，显得代码比较繁琐，finally 语句可以解决这个问题。

```java
private static void readFile(String filePath) throws MyException {
    File file = new File(filePath);
    String result;
    BufferedReader reader = null;
    try {
        reader = new BufferedReader(new FileReader(file));
        while((result = reader.readLine())!=null) {
            System.out.println(result);
        }
    } catch (IOException e) {
        System.out.println("readFile method catch block.");
        MyException ex = new MyException("read file failed.");
        ex.initCause(e);
        throw ex;
    } finally {
        System.out.println("readFile method finally block.");
        if (null != reader) {
            try {
                reader.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

调用该方法时，读取文件时若发生异常，代码会进入 catch 代码块，之后进入 finally 代码块；若读取文件时未发生异常，则会跳过 catch 代码块直接进入 finally 代码块。所以无论代码中是否发生异常，fianlly 中的代码都会执行。

若 catch 代码块中包含 return 语句，finally 中的代码还会执行吗？将以上代码中的 catch 子句修改如下：

```java
catch (IOException e) {
    System.out.println("readFile method catch block.");
    return;
}
```

调用 readFile 方法，观察当 catch 子句中调用 return 语句时，finally 子句是否执行

```java
readFile method catch block.
readFile method finally block.
```

可见，即使 catch 中包含了 return 语句，finally 子句依然会执行。若 finally 中也包含 return 语句，finally 中的 return 会覆盖前面的 return.

##### 3.6 **try-with-resource**

上面例子中，finally 中的 close 方法也可能抛出 IOException, 从而覆盖了原始异常。JAVA 7 提供了更优雅的方式来实现资源的自动释放，自动释放的资源需要是实现了 AutoCloseable 接口的类。

```java
private  static void tryWithResourceTest(){
    try (Scanner scanner = new Scanner(new FileInputStream("c:/abc"),"UTF-8")){
        // code
    } catch (IOException e){
        // handle exception
    }
}
```

try 代码块退出时，会自动调用 scanner.close 方法，和把 scanner.close 方法放在 finally 代码块中不同的是，若 scanner.close 抛出异常，则会被抑制，抛出的仍然为原始异常。被抑制的异常会由 addSusppressed 方法添加到原来的异常，如果想要获取被抑制的异常列表，可以调用 getSuppressed 方法来获取。




### **如何选择异常类型**

#### **1. 如何选择异常**
从个人开发经验来看，如果在一个应用中，需要开发一个方法(如某个功能的service方法)，这个方法如果中间可能出现异常，那么你需要考虑这个异常出现之后是否调用者可以处理，并且你是否希望调用者进行处理。

如果调用者可以处理，并且你也希望调用者进行处理，那么就要抛出受检异常，提醒调用者在使用你的方法时，考虑到如果抛出异常时如果进行处理。

相似的，如果在写某个方法时，你认为这是个偶然异常，理论上说，你觉得运行时可能会碰到什么问题，而这些问题也许不是必然发生的，也不需要调用者显示的通过异常来判断业务流程操作的，那么这时就可以使用一个RuntimeException这样的非受检异常。



#### **2. 应该选用哪种异常**
RuntimeException异常和受检异常之间的区别就是:是否强制要求调用者必须处理此异常，如果强制要求调用者必须进行处理，那么就使用受检异常，否则就选择非受检异常(RuntimeException)。一般来讲，如果没有特殊的要求，我们建议使用RuntimeException异常。



#### **3. 什么时候才需要抛异常**
首先我们需要了解一个问题，什么时候才需要抛异常？异常的设计是方便给开发者使用的，但不是乱用的。
其实这个问题很简单，如果你觉得某些”问题”解决不了了，那么你就可以抛出异常了。比如，你在写一个service,其中在写到某段代码处,你发现可能会产生问题，那么就请抛出异常吧，相信我，你此时抛出异常将是一个最佳时机。




### **常见问题**

#### **1. Error 和 Exception 区别是什么？**

Error 类型的错误通常为虚拟机相关错误，如系统崩溃，内存不足，堆栈溢出等，编译器不会对这类错误进行检测，JAVA 应用程序也不应对这类错误进行捕获，一旦这类错误发生，通常应用程序会被终止，仅靠应用程序本身无法恢复；

Exception 类的错误是可以在应用程序中进行捕获并处理的，通常遇到这种错误，应对其进行处理，使应用程序可以继续正常运行。

#### **2. 运行时异常和一般异常区别是什么？**

编译器不会对运行时异常进行检测，没有 try-catch，方法签名中也没有 throws 关键字声明，编译依然可以通过。如果出现了 RuntimeException, 那一定是程序员的错误。

一般一场如果没有 try-catch，且方法签名中也没有用 throws 关键字声明可能抛出的异常，则编译无法通过。这类异常通常为应用环境中的错误，即外部错误，非应用程序本身错误，如文件找不到等。

#### **3.NoClassDefFoundError 和 ClassNotFoundException 区别？**

NoClassDefFoundError 是一个 Error 类型的异常，是由 JVM 引起的，不应该尝试捕获这个异常。

引起该异常的原因是 JVM 或 ClassLoader 尝试加载某类时在内存中找不到该类的定义，该动作发生在运行期间，即编译时该类存在，但是在运行时却找不到了，可能是变异后被删除了等原因导致；

ClassNotFoundException 是一个受查异常，需要显式地使用 try-catch 对其进行捕获和处理，或在方法签名中用 throws 关键字进行声明。当使用 Class.forName, ClassLoader.loadClass 或 ClassLoader.findSystemClass 动态加载类到内存的时候，通过传入的类路径参数没有找到该类，就会抛出该异常；另一种抛出该异常的可能原因是某个类已经由一个类加载器加载至内存中，另一个加载器又尝试去加载它。

#### **4. JVM 是如何处理异常的？**

在一个方法中如果发生异常，这个方法会创建一个一场对象，并转交给 JVM，该异常对象包含异常名称，异常描述以及异常发生时应用程序的状态。创建异常对象并转交给 JVM 的过程称为抛出异常。可能有一系列的方法调用，最终才进入抛出异常的方法，这一系列方法调用的有序列表叫做调用栈。

JVM 会顺着调用栈去查找看是否有可以处理异常的代码，如果有，则调用异常处理代码。当 JVM 发现可以处理异常的代码时，会把发生的异常传递给它。如果 JVM 没有找到可以处理该异常的代码块，JVM 就会将该异常转交给默认的异常处理器（默认处理器为 JVM 的一部分），默认异常处理器打印出异常信息并终止应用程序。

#### **5. throw 和 throws 的区别是什么？**

throw 关键字用来抛出方法或代码块中的异常，受查异常和非受查异常都可以被抛出。
throws 关键字用在方法签名处，用来标识该方法可能抛出的异常列表。一个方法用 throws 标识了可能抛出的异常列表，调用该方法的方法中必须包含可处理异常的代码，否则也要在方法签名中用 throws 关键字声明相应的异常。

#### **6. 常见的 RuntimeException 有哪些？**

- ClassCastException(类转换异常)
- IndexOutOfBoundsException(数组越界)
- NullPointerException(空指针)
- ArrayStoreException(数据存储异常，操作数组时类型不一致)
- 还有IO操作的BufferOverflowException异常




### 常见异常

java.lang.IllegalAccessError：违法访问错误。当一个应用试图访问、修改某个类的域（Field）或者调用其方法，但是又违反域或方法的可见性声明，则抛出该异常。

java.lang.InstantiationError：实例化错误。当一个应用试图通过Java的new操作符构造一个抽象类或者接口时抛出该异常.

java.lang.OutOfMemoryError：内存不足错误。当可用内存不足以让Java虚拟机分配给一个对象时抛出该错误。

java.lang.StackOverflowError：堆栈溢出错误。当一个应用递归调用的层次太深而导致堆栈溢出或者陷入死循环时抛出该错误。

java.lang.ClassCastException：类造型异常。假设有类A和B（A不是B的父类或子类），O是A的实例，那么当强制将O构造为类B的实例时抛出该异常。该异常经常被称为强制类型转换异常。

java.lang.ClassNotFoundException：找不到类异常。当应用试图根据字符串形式的类名构造类，而在遍历CLASSPAH之后找不到对应名称的class文件时，抛出该异常。

java.lang.ArithmeticException：算术条件异常。譬如：整数除零等。

java.lang.ArrayIndexOutOfBoundsException：数组索引越界异常。当对数组的索引值为负数或大于等于数组大小时抛出。

java.lang.IndexOutOfBoundsException：索引越界异常。当访问某个序列的索引值小于0或大于等于序列大小时，抛出该异常。

java.lang.InstantiationException：实例化异常。当试图通过newInstance()方法创建某个类的实例，而该类是一个抽象类或接口时，抛出该异常。

java.lang.NoSuchFieldException：属性不存在异常。当访问某个类的不存在的属性时抛出该异常。

java.lang.NoSuchMethodException：方法不存在异常。当访问某个类的不存在的方法时抛出该异常。

java.lang.NullPointerException：空指针异常。当应用试图在要求使用对象的地方使用了null时，抛出该异常。譬如：调用null对象的实例方法、访问null对象的属性、计算null对象的长度、使用throw语句抛出null等等。

java.lang.NumberFormatException：数字格式异常。当试图将一个String转换为指定的数字类型，而该字符串确不满足数字类型要求的格式时，抛出该异常。

java.lang.StringIndexOutOfBoundsException：字符串索引越界异常。当使用索引值访问某个字符串中的字符，而该索引值小于0或大于等于序列大小时，抛出该异常。
