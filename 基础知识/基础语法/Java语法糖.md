# Java语法糖

<img src="https://img-blog.csdnimg.cn/20191103153120767.jpg" width = 50% height = 50% />

[TOC]



语法糖（Syntactic Sugar），也称糖衣语法，是由英国计算机学家 **Peter.J.Landin（彼得·兰丁）** 发明的一个术语，指在计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。**语法糖就是对现有语法的一个封装**。简而言之，**语法糖让程序更加简洁，有更高的可读性**。

> 有意思的是，在编程领域，除了语法糖，还有语法盐和语法糖精的说法，篇幅有限这里不做扩展了。

我们所熟知的编程语言中几乎都有语法糖。很多人说Java是一个“低糖语言”，其实从Java 7开始，Java语言层面上一直在添加各种糖，主要是在“Project Coin”项目下研发。尽管现在Java有人还是认为现在的Java是低糖，未来还会持续向着“高糖”的方向发展。



## 解语法糖

前面提到过，语法糖的存在主要是方便开发人员使用。但是，Java虚拟机并不支持这些语法糖。**这些语法糖在编译阶段就会被还原成简单的基础语法结构，这个过程就是解语法糖**。

Java中的语法糖只存在于编译期，在编译器将 .java 源文件编译成 .class 字节码时，如果你去看com.sun.tools.javac.main.JavaCompiler的源码，你会发现在compile()中有一个步骤就是调用desugar()，这个方法就是负责解语法糖而实现的。

Java 中最常用的语法糖主要有switch语句支持String与枚举、泛型和类型擦除、自动装箱与拆箱、方法边长参数、枚举、内部类、条件编译、断言、数值字面量、增强for循环、try-with-resources语句、Lambda表达式等。本文主要来分析下这些语法糖背后的原理。一步一步剥去糖衣，看看其本质。



## 糖块一、switch支持String与枚举

从Java 7 开始，Java语言中的语法糖在逐渐丰富，其中一个比较重要的就是Java 7中switch语句开始支持String。

在开始coding之前先科普下，Java中的swith自身原本就支持基本类型。比如int、char等。

对于int类型，直接进行数值的比较。对于char类型则是比较其ascii码。

所以，对于编译器来说，switch中其实只能使用整型，任何类型的比较都要转换成整型。比如byte。short，char(ackii码是整型)以及int。

那么接下来看下switch对String的支持，有以下代码：

```java
public class SwitchDemoString {
    public static void main(String[] args) {
        String str = "world";
        switch (str) {
        case "hello":
            System.out.println("hello");
            break;
        case "world":
            System.out.println("world");
            break;
        default:
            break;
        }
    }
}
```

反编译后内容如下：

```java
public class SwitchStringDemo {

    public static void main(String[] args) {
        String str;
        String string = str = "world";
        int n = -1;
        switch (string.hashCode()) {
            case 99162322: {
                if (!string.equals("hello")) break;
                n = 0;
                break;
            }
            case 113318802: {
                if (!string.equals("world")) break;
                n = 1;
            }
        }
        switch (n) {
            case 0: {
                System.out.println("hello");
                break;
            }
            case 1: {
                System.out.println("world");
                break;
            }
        }
    }

}
```

看到这个代码，你知道原来字符串的switch是通过equals()和hashCode()方法来实现的。还好hashCode()方法返回的是int，而不是long。

仔细看下可以发现，进行switch比较的实际是哈希值，然后通过使用equals方法比较进行安全检查，这个检查是必要的，因为哈希可能会发生碰撞。因此它的性能是不如使用枚举进行switch或者使用纯整数常量，但这也不是很差。



## 糖块二、泛型和类型擦除

我们都知道，很多语言都是支持泛型的，但是很多人不知道的是，不同的编译器对于泛型的处理方式是不同的。

通常情况下，一个编译器处理泛型有两种方式：Code specialization和Code sharing。

C++和C#是使用Code specialization的处理机制，而Java使用的是Code sharing的机制。

Code sharing方式为每个泛型类型创建唯一的字节码表示，并且将该泛型类型的实例都映射到这个唯一的字节码表示上。将多种泛型类形实例映射到唯一的字节码表示是通过类型擦除（type erasue）实现的。

也就是说，对于Java虚拟机来说，他根本不认识Map<String, String> map这样的语法。需要在编译阶段通过类型擦除的方式进行解语法糖。

类型擦除的主要过程如下：

-  将所有的泛型参数用其最左边界（最顶级的父类型）类型替换。
-  移除所有的类型参数。

以下代码：

```java
Map<String, String> map = new HashMap<String, String>();  
map.put("name", "JourWon");  
map.put("wechat", "JourWon");  
map.put("blog", "https://blog.csdn.net/ThinkWon");  
```

解语法糖之后会变成：

```java
Map map = new HashMap();  
map.put("name", "JourWon");  
map.put("wechat", "JourWon");  
map.put("blog", "https://blog.csdn.net/ThinkWon");   
```

以下代码：

```java
public static <A extends Comparable<A>> A max(Collection<A> xs) {
    Iterator<A> xi = xs.iterator();
    A w = xi.next();
    while (xi.hasNext()) {
        A x = xi.next();
        if (w.compareTo(x) < 0)
            w = x;
    }
    return w;
}
```

类型擦除后会变成：

```java
public static Comparable max(Collection xs) {
    Iterator xi = xs.iterator();
    Comparable w = (Comparable) xi.next();
    while (xi.hasNext()) {
        Comparable x = (Comparable) xi.next();
        if (w.compareTo(x) < 0)
            w = x;
    }
    return w;
}
```

虚拟机中没有泛型，只有普通类和普通方法，所有泛型类的类型参数在编译时都会被擦除，泛型类并没有自己独有的Class类对象。比如并不存在List<String>.class或是List<Integer>.class，而只有List.class。



## 糖块三、自动装箱与拆箱

自动装箱就是Java自动将原始类型值转换成对应的对象，比如将int的变量转换成Integer对象，这个过程叫做**装箱**，反之将Integer对象转换成int类型值，这个过程叫做**拆箱**。

因为这里的装箱和拆箱是自动进行的非人为转换，所以就称作为自动装箱和拆箱。

原始类型byte, short, char, int, long, float, double 和 boolean 对应的封装类为Byte, Short, Character, Integer, Long, Float, Double, Boolean。

先来看个自动装箱的代码：

```java
public static void main(String[] args) {
    int i = 10;
    Integer n = i;
}
```

反编译后代码如下:

```java
public static void main(String args[]) {
    int i = 10;
    Integer n = Integer.valueOf(i);
}
```

再来看个自动拆箱的代码：

```java
public static void main(String[] args) {
    Integer i = 10;
    int n = i;
}
```

反编译后代码如下：

```java
public static void main(String args[]) {
    Integer i = Integer.valueOf(10);
    int n = i.intValue();
}
```

从反编译得到内容可以看出，在装箱的时候自动调用的是Integer的valueOf(int)方法。而在拆箱的时候自动调用的是Integer的intValue方法。

所以，装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的 xxxValue方法实现的。



## 糖块四 、方法变长参数

可变参数(variable arguments)是在Java 1.5中引入的一个特性。它允许一个方法把任意数量的值作为参数。

看下以下可变参数代码，其中print方法接收可变参数：

```java
public static void main(String[] args) {
    print("CSDN-ThinkWon", "简书-JourWon", "博客:https://blog.csdn.net/ThinkWon");
}

public static void print(String... strs) {
    for (int i = 0; i < strs.length; i++) {
        System.out.println(strs[i]);
    }
}
```

反编译后代码：	

```java
public static void main(String[] args) {
    print(new String[]{"CSDN-ThinkWon", "\u7b80\u4e66-JourWon", "\u535a\u5ba2:https://blog.csdn.net/ThinkWon"});
}

public static void print(String[] strs) {
    for (int i = 0; i < strs.length; i++)
        System.out.println(strs[i]);
}
```

从反编译后代码可以看出，可变参数在被使用的时候，他首先会创建一个数组，数组的长度就是调用该方法时传递实参的个数，然后再把参数值全部放到这个数组当中，再把这个数组作为参数传递到被调用的方法中。



## 糖块五 、枚举

Java SE5提供了一种新的类型-Java的枚举类型，关键字enum可以将一组具名的值的有限集合创建为一种新的类型，而这些具名的值可以作为常规的程序组件使用，这是一种非常有用的功能。参考：[Java的枚举类型用法介绍](http://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=402247345&idx=1&sn=a0e1f8cc739dd8cf96ede6ec5181d244&chksm=7967d3904e105a867b1d737bc64e526756125ced5f33cc9a9b35064228f639e391d024e3abde&scene=21#wechat_redirect)

要想看源码，首先得有一个类吧，那么枚举类型到底是什么类呢？是enum吗？

答案很明显不是，enum就和class一样，只是一个关键字，他并不是一个类。

那么枚举是由什么类维护的呢，我们简单的写一个枚举：

```java
public enum T {
    SPRING,SUMMER;
}
```

然后我们使用反编译，看看这段代码到底是怎么实现的，反编译后代码内容如下：

```java
public final class T extends Enum<T> {
    public static final /* enum */ T SPRING = new T("SPRING", 0);
    public static final /* enum */ T SUMMER = new T("SUMMER", 1);
    private static final /* synthetic */ T[] $VALUES;

    public static T[] values() {
        return (T[])$VALUES.clone();
    }

    public static T valueOf(String name) {
        return Enum.valueOf(T.class, name);
    }

    private T(String string, int n) {
        super(string, n);
    }

    static {
        $VALUES = new T[]{SPRING, SUMMER};
    }
}

```

通过反编译后代码我们可以看到，public final class T extends Enum，说明，该类是继承了Enum类的，同时final关键字告诉我们，这个类也是不能被继承的。

当我们使用enmu来定义一个枚举类型的时候，编译器会自动帮我们创建一个final类型的类继承Enum类，所以枚举类型不能被继承。



## 糖块六 、内部类

内部类又称为嵌套类，可以把内部类理解为外部类的一个普通成员。

内部类之所以也是语法糖，是因为它仅仅是一个编译时的概念。

outer.java里面定义了一个内部类inner，一旦编译成功，就会生成两个完全不同的.class文件了，分别是outer.class和outer$inner.class。所以内部类的名字完全可以和它的外部类名字相同。

```java
public class OutterClass {

    private String userName;

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public static void main(String[] args) {

    }

    class InnerClass {
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }

}
```

以上代码编译后会生成两个class文件：OutterClass$InnerClass.class 、OutterClass.class 。

当我们尝试使用jad对OutterClass.class文件进行反编译的时候，命令行会打印以下内容：

```java
Parsing OutterClass.class...
Parsing inner class OutterClass$InnerClass.class...
Generating OutterClass.jad
```

它会把两个文件全部进行反编译，然后一起生成一个OutterClass.jad文件。文件内容如下：

```java
public class OutterClass {
    class InnerClass {
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        private String name;
        final OutterClass this$0;

        InnerClass() {
            this.this$0 = OutterClass.this;
            super();
        }
    }

    public OutterClass() {
    }

    public String getUserName() {
        return userName;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public static void main(String args1[]) {
    }

    private String userName;
}
```



## 糖块七 、条件编译

—般情况下，程序中的每一行代码都要参加编译。但有时候出于对程序代码优化的考虑，希望只对其中一部分内容进行编译，此时就需要在程序中加上条件，让编译器只对满足条件的代码进行编译，将不满足条件的代码舍弃，这就是条件编译。

如在C或C++中，可以通过预处理语句来实现条件编译。其实在Java中也可实现条件编译。我们先来看一段代码：

```java
public class ConditionalCompilation {
    public static void main(String[] args) {
        final boolean DEBUG = true;
        if(DEBUG) {
            System.out.println("Hello, DEBUG!");
        }

        final boolean ONLINE = false;

        if(ONLINE){
            System.out.println("Hello, ONLINE!");
        }
    }
}
```

反编译后代码如下：

```java
public class ConditionalCompilation {
    public static void main(String[] args) {
    boolean DEBUG = true;
    
    System.out.println("Hello, DEBUG!");
    
    boolean ONLINE = false;
}
```

首先，我们发现，在反编译后的代码中没有System.out.println("Hello, ONLINE!");，这其实就是条件编译。

当if(ONLINE)为false的时候，编译器就没有对其内的代码进行编译。

所以，Java语法的条件编译，是通过判断条件为常量的if语句实现的。根据if判断条件的真假，编译器直接把分支为false的代码块消除。通过该方式实现的条件编译，必须在方法体内实现，而无法在正整个Java类的结构或者类的属性上进行条件编译。

这与C/C++的条件编译相比，确实更有局限性。在Java语言设计之初并没有引入条件编译的功能，虽有局限，但是总比没有更强。



## 糖块八 、断言

在Java中，assert关键字是从JAVA SE 1.4 引入的，为了避免和老版本的Java代码中使用了assert关键字导致错误，Java在执行的时候默认是不启动断言检查的（这个时候，所有的断言语句都将忽略）。

如果要开启断言检查，则需要用开关-enableassertions或-ea来开启。

看一段包含断言的代码：

```java
public class AssertTest {
    public static void main(String args[]) {
        int a = 1;
        int b = 1;
        assert a == b;
        System.out.println("CSDN-ThinkWon");
        assert a != b : "ThinkWon";
        System.out.println("博客:https://blog.csdn.net/ThinkWon");
    }
}
```

反编译后代码如下：

```java
public class AssertTest {
	static final /* synthetic */ boolean $assertionsDisabled;

    public static void main(String[] args) {
        boolean a = true;
        boolean b = true;
        if (!$assertionsDisabled && a != b) {
            throw new AssertionError();
        }
        System.out.println("CSDN-ThinkWon");
        if (!$assertionsDisabled && a == b) {
            throw new AssertionError((Object)"ThinkWon");
        }
        System.out.println("\u535a\u5ba2:https://blog.csdn.net/ThinkWon");
    }

    static {
        $assertionsDisabled = !AssertTest.class.desiredAssertionStatus();
    }

}
```

很明显，反编译之后的代码要比我们自己的代码复杂的多。所以，使用了assert这个语法糖我们节省了很多代码。

其实断言的底层实现就是if语言，如果断言结果为true，则什么都不做，程序继续执行，如果断言结果为false，则程序抛出AssertError来打断程序的执行。

-enableassertions会设置$assertionsDisabled字段的值。



## 糖块九 、数值字面量

在java 7中，数值字面量，不管是整数还是浮点数，都允许在数字之间插入任意多个下划线。这些下划线不会对字面量的数值产生影响，目的就是方便阅读。

比如：

```java
public class Test {
    public static void main(String[] args) {
        int i = 10_000;
        System.out.println(i);
    }
}
```

反编译后：

```java
public class Test {
    public static void main(String[] args) {
        int i = 10000;
        System.out.println(i);
    }
}
```

反编译后就是把删除了`_`。也就是说编译器并不认识在数字字面量中的`_`，需要在编译阶段把他去掉。



## 糖块十 、增强for循环

增强for循环（for-each）相信大家都不陌生，日常开发经常会用到的，他会比for循环要少写很多代码，那么这个语法糖背后是如何实现的呢？

```java
public static void main(String args[]) {
    String[] strs = {"CSDN-ThinkWon", "简书-JourWon", "博客:https://blog.csdn.net/ThinkWon"};
    for (String s : strs) {
        System.out.println(s);
    }
    System.out.println();

    List<String> strList = Arrays.asList(strs);
    for (String s : strList) {
        System.out.println(s);
    }
}
```

反编译后代码如下：

```java
public static void main(String args[]) {
    String[] strs;
    String[] arrstring = strs = new String[]{"CSDN-ThinkWon", "\u7b80\u4e66-JourWon", "\u535a\u5ba2:https://blog.csdn.net/ThinkWon"};
    int n = arrstring.length;
    for (int i = 0; i < n; ++i) {
        String s = arrstring[i];
        System.out.println(s);
    }
    System.out.println();

    List<String> strList = Arrays.asList(strs);
    Iterator<String> iterator = strList.iterator();
    while (iterator.hasNext()) {
        String s = iterator.next();
        System.out.println(s);
    }

}
```

代码很简单，for-each的实现原理其实就是使用了普通的for循环和迭代器。



## 糖块十一 、try-with-resource语句

Java里，对于文件操作IO流、数据库连接等开销非常昂贵的资源，用完之后必须及时通过close方法将其关闭，否则资源会一直处于打开状态，可能会导致内存泄露等问题。

关闭资源的常用方式就是在finally块里释放，即调用close方法。比如，我们经常会写这样的代码：

```java
public static void main(String args[]) {
    BufferedReader br = null;
    try {
        String line;
        br = new BufferedReader(new FileReader("d:\\hello.xml"));
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        // handle exception
    } finally {
        try {
            if (br != null) {
                br.close();
            }
        } catch (IOException ex) {
            // handle exception
        }
    }
}
```

从Java 7开始，jdk提供了一种更好的方式关闭资源，使用try-with-resources语句，改写一下上面的代码，效果如下：

```java
public static void main(String args[]) {
    try (BufferedReader br = new BufferedReader(new FileReader("d:\\ hello.xml"))) {
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }
    } catch (IOException e) {
        // handle exception
    }
}
```

看，这简直是一大福音啊，虽然我之前一般使用IOUtils去关闭流，并不会使用在finally中写很多代码的方式，但是这种新的语法糖看上去好像优雅很多呢。

反编译以上代码，看下他的背后原理：

```java
public static void main(String[] args) {
    try {
        BufferedReader br = new BufferedReader(new FileReader("d:\\ hello.xml"));
        Throwable throwable = null;
        try {
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (Throwable line) {
            throwable = line;
            throw line;
        } finally {
            if (br != null) {
                if (throwable != null) {
                    try {
                        br.close();
                    } catch (Throwable line) {
                        throwable.addSuppressed(line);
                    }
                } else {
                    br.close();
                }
            }
        }
    } catch (IOException br) {
        // empty catch block
    }
}
```

其实背后的原理也很简单，那些我们没有做的关闭资源的操作，编译器都帮我们做了。

所以，再次印证了，语法糖的作用就是方便程序员的使用，但最终还是要转成编译器认识的语言。



## 糖块十二、Lambda表达式

关于Labmda表达式，有人可能会有质疑，因为网上有人说他并不是语法糖。其实我想纠正下这个说法。

Labmda表达式不是匿名内部类的语法糖，但是他也是一个语法糖。实现方式其实是依赖了几个JVM底层提供的Labmda相关api。

先来看一个简单的Labmda表达式。遍历一个list：

```java
public static void main(String[] args) {
    List<String> strList = new ArrayList<>();
    strList.add("CSDN-ThinkWon");
    strList.add("简书-JourWon");
    strList.add("博客:https://blog.csdn.net/ThinkWon");

    strList.forEach(s -> System.out.println(s));
}
```

为啥说他并不是内部类的语法糖呢，前面讲内部类我们说过，内部类在编译之后会有两个class文件，但是，包含Labmda表达式的类编译后只有一个文件。

反编译后代码如下:

```java
public static void main(String[] args) {
    ArrayList<String> strList = new ArrayList<String>();
    strList.add("CSDN-ThinkWon");
    strList.add("\u7b80\u4e66-JourWon");
    strList.add("\u535a\u5ba2:https://blog.csdn.net/ThinkWon");
    strList.forEach((Consumer<String>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)V, lambda$main$0(java.lang.String ), (Ljava/lang/String;)V)());
}

private static /* synthetic */ void lambda$main$0(String s) {
    System.out.println(s);
}
```

可以看到，在forEach方法中，其实是调用了java.lang.invoke.LambdaMetafactory#metafactory方法，该方法的第四个参数implMethod指定了方法实现。可以看到这里其实是调用了一个lambda$main$0方法进行了输出。

再来看一个稍微复杂一点的，先对List进行过滤，然后再输出：

```java
public static void main(String[] args) {
    List<String> strList = new ArrayList<>();
    strList.add("CSDN-ThinkWon");
    strList.add("简书-JourWon");
    strList.add("博客:https://blog.csdn.net/ThinkWon");

    List list = strList.stream().filter(string -> string.contains("CSDN-ThinkWon")).collect(Collectors.toList());

    list.forEach(s -> {
        System.out.println(s);
    });
}
```

反编译后代码如下：

```java
public static void main(String[] args) {
    ArrayList<String> strList = new ArrayList<String>();
    strList.add("CSDN-ThinkWon");
    strList.add("\u7b80\u4e66-JourWon");
    strList.add("\u535a\u5ba2:https://blog.csdn.net/ThinkWon");
    List<Object> list = strList.stream().filter((Predicate<String>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)Z, lambda$main$0(java.lang.String ), (Ljava/lang/String;)Z)()).collect(Collectors.toList());
    list.forEach((Consumer<Object>)LambdaMetafactory.metafactory(null, null, null, (Ljava/lang/Object;)V, lambda$main$1(java.lang.Object ), (Ljava/lang/Object;)V)());
}

private static /* synthetic */ void lambda$main$1(Object s) {
    System.out.println(s);
}

private static /* synthetic */ boolean lambda$main$0(String string) {
    return string.contains("CSDN-ThinkWon");
}
```

两个Labmda表达式分别调用了lambda$main$1和lambda$main$0两个方法。

所以，Labmda表达式的实现其实是依赖了一些底层的api，在编译阶段，编译器会把Labmda表达式进行解糖，转换成调用内部api的方式。



## 可能遇到的坑

#### 泛型-当泛型遇到重载

```java
public class GenericTypes {

    public static void method(List<String> list) {  
        System.out.println("invoke method(List<String> list)");  
    }  

    public static void method(List<Integer> list) {  
        System.out.println("invoke method(List<Integer> list)");  
    }  
}  
```

上面这段代码，有两个重载的函数，因为他们的参数类型不同，一个是List<String>，另一个是List<Integer>，但是，这段代码是编译通不过的。因为我们前面讲过，参数List<String>和List<Integer>编译之后都被擦除了，变成了一样的原生类型List，擦除动作导致这两个方法的特征签名变得一模一样。	



#### 泛型-当泛型遇到catch

泛型的类型参数不能用在Java异常处理的catch语句中。因为异常处理是由JVM在运行时刻来进行的。由于类型信息被擦除，JVM是无法区分两个异常类型MyException<String>和MyException<Integer>的



#### 泛型-当泛型内包含静态变量

```java
public class StaticTest{
    public static void main(String[] args) {
        GT<Integer> gti = new GT<Integer>();
        gti.var = 1;
        GT<String> gts = new GT<String>();
        gts.var = 2;
        System.out.println(gti.var);
    }

    class GT<T> {
        public static int var = 0;

        public void nothing(T x) {
        }
    }
}
```

这段代码编译都无法通过，因为泛型里面不能引用静态变量。由于经过类型擦除，所有的泛型类实例都关联到同一份字节码上，泛型类的所有静态变量是共享的。



#### 自动装箱与拆箱-对象相等比较

```java
public static void main(String[] args) {
    Integer a = 1000;
    Integer b = 1000;
    Integer c = 100;
    Integer d = 100;
    System.out.println("a == b is " + (a == b));
    System.out.println(("c == d is " + (c == d)));
}
```

输出结果：

```java
a == b is false
c == d is true
```

在Java 5中，在Integer的操作上引入了一个新功能来节省内存和提高性能。整型对象通过使用相同的对象引用实现了缓存和重用。

> 适用于整数值区间-128 至 +127。
>
> 只适用于自动装箱。使用构造函数创建对象不适用。



#### 增强for循环

```java
for (Student stu : students) {    
    if (stu.getId() == 2) {
        students.remove(stu);  
    }           
}
```

会抛出ConcurrentModificationException异常。

Iterator是工作在一个独立的线程中，并且拥有一个 mutex 锁。 Iterator被创建之后会建立一个指向原来对象的单链索引表，当原来的对象数量发生变化时，这个索引表的内容不会同步改变，所以当索引指针往后移动的时候就找不到要迭代的对象，所以按照 fail-fast 原则 Iterator 会马上抛出java.util.ConcurrentModificationException异常。

所以 Iterator 在工作的时候是不允许被迭代的对象被改变的。但你可以使用 Iterator 本身的方法remove()来删除对象，Iterator.remove() 方法会在删除当前迭代对象的同时维护索引的一致性。



## 总结

前面介绍了12种Java中常用的语法糖。所谓语法糖就是提供给开发人员便于开发的一种语法而已。但是这种语法只有开发人员认识。要想被执行，需要进行解糖，即转成JVM认识的语法。

当我们把语法糖解糖之后，你就会发现其实我们日常使用的这些方便的语法，其实都是一些其他更简单的语法构成的。有了这些语法糖，我们在日常开发的时候可以大大提升效率，但是同时也要避免过渡使用。使用之前最好了解下原理，避免掉坑。
