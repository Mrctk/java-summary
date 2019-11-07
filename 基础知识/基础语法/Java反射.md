# Java反射

![反射](https://img-blog.csdnimg.cn/20190828225708126.png)

[toc]



## 概述

### 定义

**Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意方法和属性**。这种动态获取信息以及动态调用对象方法的功能称为Java语言的反射机制。



### 用途

通过反射，Java 代码可以发现有关已加载类的字段，方法和构造函数的信息，并可以在安全限制内对这些字段，方法和构造函数进行操作。

很多人都认为反射在实际Java中开发应用中并不广泛，其实不然。当我们在使用 IDE（如 IDEA/Eclipse）时，当我们输入一个对象或者类并调用它的属性和方法时，一按 (“.”)点号，编译器就会自动列出她的属性或方法，这里就会用到反射。

**反射最重要的用途就是开发各种通用框架**

很多框架（比如 Spring）都是配置化的（比如Spring 通过 XML 配置模式装载 Bean），为了保证框架的通用性，他们可能根据配置文件加载不同的对象或类，调用不同的方法，这个时候就必须用到反射——运行时动态加载需要加载的对象。

对于框架开发人员来说，反射作用非常大，它是各种容器实现的核心。而对于一般的开发者来说，不深入框架开发反射用的就会少一点，不过了解一下框架的底层机制有助于丰富自己的编程思想，也是很有益的。



Java反射框架提供一下功能：

- 在运行时判定任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判定任意一个类所具有的成员变量和方法
- 在运行时调用任意一个对象的方法



### 反射的优缺点

#### 反射的优点

使用反射机制，代码可以在运行时装配，提高了程序的灵活性和扩展性，降低耦合性，提高自适应能力。它允许程序创建和控制任何类的对象，无需硬编码目标类

#### 反射的缺点

性能问题：使用反射基本上是一种解释操作，JVM无法对这些代码进行优化，因此，反射操作的效率要比那些非反射操作低得多。反射机制主要应用在对灵活性和扩展性要求很高的系统框架上，对性能要求高的程序中不建议使用

安全限制：使用反射技术要求程序必须在一个没有安全限制的环境中运行

内部暴露：由于反射允许代码执行一些在正常情况下不被允许的操作（比如访问私有的属性和方法），所以使用反射可能会导致意料之外的副作用——代码有功能上的错误，降低可移植性。反射代码破坏了抽象性，因此当平台发生改变的时候，代码的行为就有可能也随着变化。



### 反射机制的相关类

与Java反射相关的类如下：

| 类名          | 用途                                             |
| ------------- | ------------------------------------------------ |
| Class类       | 代表类的实体，在运行的Java应用程序中表示类和接口 |
| Field类       | 代表类的成员变量（成员变量也称为类的属性）       |
| Method类      | 代表类的方法                                     |
| Constructor类 | 代表类的构造方法                                 |



## Class类

Class代表类的实体，在运行的Java应用程序中表示类和接口。在这个类中提供了很多有用的方法，这里对他们简单的分类介绍。

- **获得类相关的方法**

| 方法                       | 用途                                                   |
| -------------------------- | ------------------------------------------------------ |
| asSubclass(Class<U> clazz) | 把传递的类的对象转换成代表其子类的对象                 |
| Cast                       | 把对象转换成代表类或是接口的对象                       |
| getClassLoader()           | 获得类的加载器                                         |
| getClasses()               | 返回一个数组，数组中包含该类中所有公共类和接口类的对象 |
| getDeclaredClasses()       | 返回一个数组，数组中包含该类中所有类和接口类的对象     |
| forName(String className)  | 根据类名返回类的对象                                   |
| getName()                  | 获得类的完整路径名字                                   |
| newInstance()              | 创建类的实例                                           |
| getPackage()               | 获得类的包                                             |
| getSimpleName()            | 获得类的名字                                           |
| getSuperclass()            | 获得当前类继承的父类的名字                             |
| getInterfaces()            | 获得当前类实现的类或是接口                             |

- **获得类中属性相关的方法**

| 方法                          | 用途                   |
| ----------------------------- | ---------------------- |
| getField(String name)         | 获得某个公有的属性对象 |
| getFields()                   | 获得所有公有的属性对象 |
| getDeclaredField(String name) | 获得某个属性对象       |
| getDeclaredFields()           | 获得所有属性对象       |

- **获得类中注解相关的方法**

| 方法                                            | 用途                                   |
| ----------------------------------------------- | -------------------------------------- |
| getAnnotation(Class<A> annotationClass)         | 返回该类中与参数类型匹配的公有注解对象 |
| getAnnotations()                                | 返回该类所有的公有注解对象             |
| getDeclaredAnnotation(Class<A> annotationClass) | 返回该类中与参数类型匹配的所有注解对象 |
| getDeclaredAnnotations()                        | 返回该类所有的注解对象                 |

- **获得类中构造器相关的方法**

| 方法                                               | 用途                                   |
| -------------------------------------------------- | -------------------------------------- |
| getConstructor(Class...<?> parameterTypes)         | 获得该类中与参数类型匹配的公有构造方法 |
| getConstructors()                                  | 获得该类的所有公有构造方法             |
| getDeclaredConstructor(Class...<?> parameterTypes) | 获得该类中与参数类型匹配的构造方法     |
| getDeclaredConstructors()                          | 获得该类所有构造方法                   |

- **获得类中方法相关的方法**

| 方法                                                       | 用途                   |
| ---------------------------------------------------------- | ---------------------- |
| getMethod(String name, Class...<?> parameterTypes)         | 获得该类某个公有的方法 |
| getMethods()                                               | 获得该类所有公有的方法 |
| getDeclaredMethod(String name, Class...<?> parameterTypes) | 获得该类某个方法       |
| getDeclaredMethods()                                       | 获得该类所有方法       |

- **类中其他重要的方法**

| 方法                                                         | 用途                             |
| ------------------------------------------------------------ | -------------------------------- |
| isAnnotation()                                               | 如果是注解类型则返回true         |
| isAnnotationPresent(Class<? extends Annotation> annotationClass) | 如果是指定类型注解类型则返回true |
| isAnonymousClass()                                           | 如果是匿名类则返回true           |
| isArray()                                                    | 如果是一个数组类则返回true       |
| isEnum()                                                     | 如果是枚举类则返回true           |
| isInstance(Object obj)                                       | 如果obj是该类的实例则返回true    |
| isInterface()                                                | 如果是接口类则返回true           |
| isLocalClass()                                               | 如果是局部类则返回true           |
| isMemberClass()                                              | 如果是内部类则返回true           |



## Field类

Field代表类的成员变量（成员变量也称为类的属性）。

| 方法                          | 用途                    |
| ----------------------------- | ----------------------- |
| equals(Object obj)            | 属性与obj相等则返回true |
| get(Object obj)               | 获得obj中对应的属性值   |
| set(Object obj, Object value) | 设置obj中对应属性值     |



## Method类

Method代表类的方法。

| 方法                               | 用途                                     |
| ---------------------------------- | ---------------------------------------- |
| invoke(Object obj, Object... args) | 传递object对象及参数调用该对象对应的方法 |



## Constructor类

Constructor代表类的构造方法。

| 方法                            | 用途                       |
| ------------------------------- | -------------------------- |
| newInstance(Object... initargs) | 根据传递的参数创建类的对象 |



## 示例

为了演示反射的使用，首先构造一个与书籍相关的model——Book.java，然后了解获取Class类对象的三种方法，最后通过反射方法示例创建对象、反射私有构造方法、反射私有属性、反射私有方法



被反射类Book.java

```java
public class Book{
    private final static String TAG = "BookTag";

    private String name;
    private String author;

    @Override
    public String toString() {
        return "Book{" +
                "name='" + name + '\'' +
                ", author='" + author + '\'' +
                '}';
    }

    public Book() {
    }

    private Book(String name, String author) {
        this.name = name;
        this.author = author;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    private String declaredMethod(int index) {
        String string = null;
        switch (index) {
            case 0:
                string = "I am declaredMethod 0 !";
                break;
            case 1:
                string = "I am declaredMethod 1 !";
                break;
            default:
                string = "I am declaredMethod -1 !";
        }

        return string;
    }
}
```



### 获取Class类对象的三种方法

在反射中，要获取一个类或调用一个类的方法，我们首先需要获取到该类的 Class 对象。
在 Java API 中，获取 Class 类对象的三种方法
方法一：使用 Class.forName 静态方法。当你知道该类的全路径名时，你可以使用该方法获取 Class 类对象。但可能抛出 ClassNotFoundException 异常
Class<?> classBook = Class.forName("com.jourwon.reflect.Book");

方法二：这种方法只适合在编译前就知道操作的 Class。直接通过 类名.class 的方式得到，该方法最为安全可靠，程序性能更高，这说明任何一个类都有一个隐含的静态成员变量 class
Class clz = Book.class;

方法三：使用类对象的 getClass() 方法。
Book book1 = new Book();
Class<? extends Book> book1Class = book1.getClass();

```java
@Test
public void getClz() {
    try {
        // 方法一，使用 Class.forName 静态方法
        Class<?> classBook = Class.forName("com.jourwon.reflect.Book");

        // 方法二：直接通过 类名.class 的方式得到
        Class clz = Book.class;

        // 方法三：使用类对象的 getClass() 方法。
        Book book1 = new Book();
        Class<? extends Book> book1Class = book1.getClass();

        System.out.println(classBook == clz);
        System.out.println(classBook == book1Class);

    } catch (Exception ex) {
        ex.printStackTrace();
    }
}
```

输出结果都是true，可以知道三种方法获取到的Class对象都是同一个对象

```java
true
true
```



### 反射常用类和方法测试

```java
public class ReflectClass {

    private static final Logger log = LoggerFactory.getLogger(ReflectClass.class);

    // 创建对象
    @Test
    public void reflectNewInstance() {
        try {
            Class<?> classBook = Class.forName("com.jourwon.reflect.Book");

            Object objectBook = classBook.newInstance();
            Book book = (Book) objectBook;
            book.setName("Java高级特性-反射-创建对象");
            book.setAuthor("JourWon");
            log.info(book.toString());

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // 反射私有的构造方法
    @Test
    public void reflectPrivateConstructor() {
        try {
            Class<?> classBook = Class.forName("com.jourwon.reflect.Book");
            Constructor<?> declaredConstructorBook = classBook.getDeclaredConstructor(String.class, String.class);
            // 暴力反射
            declaredConstructorBook.setAccessible(true);
            Object objectBook = declaredConstructorBook.newInstance("Java高级特性-反射-反射私有的构造方法", "JourWon");
            Book book = (Book) objectBook;
            log.info(book.toString());
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    // 反射私有属性
    @Test
    public void reflectPrivateField() {
        try {
            Class<?> classBook = Class.forName("com.jourwon.reflect.Book");
            Object objectBook = classBook.newInstance();
            Field fieldTag = classBook.getDeclaredField("TAG");
            fieldTag.setAccessible(true);
            String tag = (String) fieldTag.get(objectBook);
            log.info(tag);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }


    // 反射私有方法
    @Test
    public void reflectPrivateMethod() {
        try {
            Class<?> classBook = Class.forName("com.jourwon.reflect.Book");
            Method methodBook = classBook.getDeclaredMethod("declaredMethod", int.class);
            methodBook.setAccessible(true);
            Object objectBook = classBook.newInstance();
            String string = (String) methodBook.invoke(objectBook, 0);

            ReflectClass.

            log.info(string);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }


}
```

输出结果

```java
[main] INFO com.jourwon.reflect.ReflectClass - Book{name='Java高级特性-反射-创建对象', author='JourWon'}

[main] INFO com.jourwon.reflect.ReflectClass - Book{name='Java高级特性-反射-反射私有的构造方法', author='JourWon'}

[main] INFO com.jourwon.reflect.ReflectClass - BookTag

[main] INFO com.jourwon.reflect.ReflectClass - I am declaredMethod 0 ! 
```

**通过反射获取私有属性，方法和构造方法时，需要进行暴力反射，设置setAccessible(true)**。否则会报错说无法获取私有属性，方法和构造方法



## 总结

本文列举了反射机制使用过程中常用的、重要的一些类及其方法，更多信息和用法，反射原理和源码分析需要近一步的阅读反射相关资料。

在阅读Class类文档时发现一个特点，以通过反射获得Method对象为例，一般会提供四种方法，getMethod(parameterTypes)、getMethods()、getDeclaredMethod(parameterTypes)和getDeclaredMethods()。getMethod(parameterTypes)用来获取某个公有的方法的对象，getMethods()获得该类所有公有的方法，getDeclaredMethod(parameterTypes)获得该类某个方法，getDeclaredMethods()获得该类所有方法。**带有Declared修饰的方法可以反射到私有的方法，没有Declared修饰的只能用来反射公有的方法**。其他的Annotation、Field、Constructor也是如此。

