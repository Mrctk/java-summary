# Java注解

![詹姆斯·高斯林](https://raw.githubusercontent.com/JourWon/image/master/Java简介/詹姆斯·高斯林2.jpg)

## 为什么要引入注解？

使用【注解】之前(甚至在使用之后)，【XML】被广泛的应用于描述元数据，得到各大框架的青睐，它以松耦合的方式完成了框架中几乎所有的配置，但是随着项目越来越庞大，【XML】的内容也越来越复杂，一些应用开发人员和架构师发现维护成本变高。他们希望使用一些和代码紧耦合的东西，于是就有人提出来一种标记式高耦合的配置方式【注解】。方法上可以进行注解，类上也可以注解，字段属性上也可以注解，反正几乎需要配置的地方都可以进行注解。

下面我们通过一个例子来理解这两者的区别。

假如你想为应用设置很多的常量或参数，这种情况下，【XML】是一个很好的选择，因为它不会同特定的代码耦合。如果你想把某个方法声明为服务，那么使用【注解】会更好一些，因为这种情况下需要注解和方法紧密耦合起来，开发人员也必须认识到这点。

同时，【注解】定义了一种标准的描述元数据的方式。

关于【注解】和【XML】两种不同的配置模式，争论了好多年，各有各的优劣，注解可以提供更大的便捷性，易于维护修改，但耦合度高，而 【XML】 相对于注解则是相反的。追求低耦合就要抛弃高效率，追求效率必然会遇到耦合。目前，许多框架将【XML】和【注解】两种方式结合使用，平衡两者之间的利弊。

本文不再辨析两者谁优谁劣，而在于以最简单的语言介绍注解相关的基本知识。



## 什么是注解

注解也叫**元数据**，即一种描述数据的数据。例如我们常见的@Override和@Deprecated，注解是JDK1.5版本开始引入的一个特性，用于对代码进行说明，可以对包、类、接口、字段、方法参数、局部变量等进行注解

Annotation接口中有下面这句话来描述注解：Annotation 是所有注解继承的公共接口

```java
The common interface extended by all annotation types.
```

注解的本质就是一个继承了 Annotation 接口的接口。有关这一点，你可以去反编译任意一个注解类得到结果。

**一个注解准确意义上来说，只不过是一种特殊的注释而已，如果没有解析它的代码，它可能连注释都不如。**

而解析一个类或者方法的注解往往有两种形式，一种是编译期直接的扫描，一种是运行期反射。反射的事情我们先不讨论，而编译器的扫描指的是编译器在对 Java 代码编译成字节码的过程中会检测到某个类或者方法被一些注解修饰，这时它就会对于这些注解进行某些处理。

```java
@Override
public String toString() {
    return "Hello Annotation";
}
```

上面的代码中，我重写了`toString()`方法并使用了`@Override`注解。但是，即使我不使用`@Override`注解标记代码，程序也能够正常执行。那么，该注解表示什么？这么写有什么好处吗？事实上，`@Override`告诉编译器这个方法是一个重写方法(描述方法的元数据)，如果父类中不存在该方法，编译器便会报错，提示该方法没有重写父类中的方法。如果我不小心拼写错误，例如将`toString()`写成了`toStrring()`，而且我也没有使用`@Override`注解，那程序依然能编译运行。但运行结果会和我期望的大不相同。现在我们了解了什么是注解，并且使用注解有助于提高代码的可读性。



## 注解的用途

1. 生成文档，通过代码里标识的元数据生成javadoc文档。
2. 编译检查，通过代码里标识的元数据让编译器在编译期间进行检查验证。
3. 编译时动态处理，编译时通过代码里标识的元数据动态处理，例如动态生成代码。
4. 运行时动态处理，运行时通过代码里标识的元数据动态处理，例如使用反射注入实例



## 注解的分类

1. **Java自带的标准注解**，包括@Override（标明重写某个方法）、@Deprecated（标明某个类或方法过时）和@SuppressWarnings（标明要忽略的警告），使用这些注解后编译器就会进行检查
2. **元注解，元注解是用于定义注解的注解**，包括@Retention（标明注解被保留的阶段）、@Target（标明注解使用的范围）、@Inherited（标明注解可继承）、@Documented（标明是否生成javadoc文档）
3. **自定义注解**，可以根据自己的需求定义注解



## 元注解

要想真正掌握怎么使用注解，还需要先学习一下元注解。

元注解是用于修饰注解的注解	

元注解有 @Retention、@Documented、@Target、@Inherited、@Repeatable 5 种。

### @Retention

Retention 的英文意为保留期的意思。当 @Retention 应用到一个注解上的时候，它解释说明了这个注解的的存活时间。

它的取值如下：

- RetentionPolicy.SOURCE 注解只在源码阶段保留，在编译器进行编译时它将被丢弃忽视。
- RetentionPolicy.CLASS 注解只被保留到编译进行的时候，它并不会被加载到 JVM 中。如Java内置注解，@Override、@Deprecated、@SuppressWarnning等
- RetentionPolicy.RUNTIME 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们。如SpringMvc中的@Controller、@Autowired、@RequestMapping等。



### @Documented

顾名思义，这个元注解肯定是和文档有关。它的作用是能够将注解中的元素包含到 Javadoc 中去。



### @Target

Target 是目标的意思，@Target 指定了注解运用的地方。

你可以这样理解，当一个注解被 @Target 注解时，这个注解就被限定了运用的场景。

类比到标签，原本标签是你想张贴到哪个地方就到哪个地方，但是因为 @Target 的存在，它张贴的地方就非常具体了，比如只能张贴到方法上、类上、方法参数上等等。@Target 有下面的取值

- ElementType.ANNOTATION_TYPE 可以给一个注解进行注解
- ElementType.CONSTRUCTOR 可以给构造方法进行注解
- ElementType.FIELD 可以给属性进行注解
- ElementType.LOCAL_VARIABLE 可以给局部变量进行注解
- ElementType.METHOD 可以给方法进行注解
- ElementType.PACKAGE 可以给一个包进行注解
- ElementType.PARAMETER 可以给一个方法内的参数进行注解
- ElementType.TYPE 可以给一个类型进行注解，比如类、接口、枚举



### @Inherited

Inherited 是继承的意思，但是它并不是说注解本身可以继承，而是说如果一个超类使用了@Inherited 注解，那么如果它的子类没有被任何注解应用的话，那么这个子类就继承了超类的注解。 

说的比较抽象。代码来解释。



### @Repeatable

Repeatable 自然是可重复的意思。@Repeatable 是 Java 1.8 才加进来的，所以算是一个新的特性。

Repeatable使用场景：在需要对同一种注解多次使用时，往往需要借助@Repeatable。


下面举例说明一下，在生活中一个人往往是具有多种身份，如果我把每种身份当成一种注解该如何使用

先声明一个Persons类用来包含所有的身份

```java
@Target(ElementType.TYPE) 
@Retention(RetentionPolicy.RUNTIME)
public @interface Persons {
	Person[] value();
}
```

这里@Target是声明Persons注解的作用范围，参数ElementType.Type代表可以给一个类进行注解

@Retention是注解的有效时间，RetentionPolicy.RUNTIME是指程序运行的时候。

 

Person注解

```java
@Repeatable(Persons.class)
public @interface Person{
	String role() default "";
}
```

@Repeatable括号内的就相当于用来保存该注解内容的容器。

 

声明一个Man类，给该类加上一些身份。

```java
@Person(role="CEO")
@Person(role="husband")
@Person(role="father")
@Person(role="son")
public   class Man {
	String name="";
}
```



在主方法中访问该注解。

```java
public static void main(String[] args) {
    Annotation[] annotations = Man.class.getAnnotations();  
    System.out.println(annotations.length);
    Persons p1=(Persons) annotations[0];
    for(Person t:p1.value()){
    	System.out.println(t.role());
    }
}
```

运行结果

```java
1
CEO
husband
father
son
```





## 注解的属性

注解的属性也叫做成员变量。注解只有成员变量，没有方法。注解的成员变量在注解的定义中以“无形参的方法”形式来声明，其方法名定义了该成员变量的名字，其返回值定义了该成员变量的类型。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface TestAnnotation{
    
    int id();
    
    String msg();

}
```

上面代码定义了 @TestAnnotation 这个注解中拥有 id 和 msg 两个属性。在使用的时候，我们应该给它们进行赋值。

赋值的方式是在注解的括号内以 value="" 形式，多个属性之前用 ，隔开。

```java
@TestAnnotation(id=3, msg="hello annotation")
public class Test {

}
```



## 快捷方式

所谓的快捷方式就是注解中定义了名为value的元素，并且在使用该注解时，如果该元素是唯一需要赋值的一个元素，那么此时无需使用key=value的语法，而只需在括号内给出value元素所需的值即可。这可以应用于任何合法类型的元素，记住，这限制了元素名必须为value，简单案例如下

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@interface IntegerVaule{
   int value() default 0;
   String name() default "";
}

public class QuicklyWay {
   
   @IntegerVaule(20)
   public int age;
   
   @IntegerVaule(value = 10000, name = "MONEY")
   public int money;
 
}
```



## 注解不支持继承

注解是不支持继承的，因此不能使用关键字extends来继承某个@interface，但注解在编译后，编译器会自动继承java.lang.annotation.Annotation接口



## 声明注解

这里总共定义了4个注解来演示注解的声明

1. 定义一个可以注解在Class，interface，enum上的注解
2. 定义一个可以注解在METHOD上的注解
3. 定义一个可以注解在FIELD上的注解
4. 定义一个可以注解在PARAMETER上的注解



```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnTargetType {

    /**
     * 定义注解的一个元素 并给定默认值
     * @return
     */
    String value() default "定义在类接口枚举类上的注解元素value的默认值";

}
```



```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnTargetMethod {

    /**
     * 定义注解的一个元素 并给定默认值
     * @return
     */
    String value() default "定义在方法上的注解元素value的默认值";

}
```



```java
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnTargetField {

    /**
     * 定义注解的一个元素 并给定默认值
     * @return
     */
    String value() default "定义在字段上的注解元素value的默认值";

}
```



```java
@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
public @interface MyAnTargetParameter {

    /**
     * 定义注解的一个元素 并给定默认值
     * @return
     */
    String value() default "定义在参数上的注解元素value的默认值";

}
```



编写一个测试处理类处理以上注解

```java
@MyAnTargetType
public class AnnotationTest {

    @MyAnTargetField
    private String field = "我是字段";

    @MyAnTargetMethod("测试方法")
    public void test(@MyAnTargetParameter String args) {
        System.out.println("参数值 === " + args);
    }

    public static void main(String[] args) {
        // 获取类上的注解MyAnTargetType
        MyAnTargetType t = AnnotationTest.class.getAnnotation(MyAnTargetType.class);
        System.out.println("类上的注解值 === " + t.value());
        MyAnTargetMethod tm = null;
        try {
            // 根据反射获取AnnotationTest类上的test方法
            Method method = AnnotationTest.class.getDeclaredMethod("test", String.class);
            // 获取方法上的注解MyAnTargetMethod
            tm = method.getAnnotation(MyAnTargetMethod.class);
            System.out.println("方法上的注解值 === " + tm.value());
            // 获取方法上的所有参数注解  循环所有注解找到MyAnTargetParameter注解
            Annotation[][] annotations = method.getParameterAnnotations();
            for (Annotation[] tt : annotations) {
                for (Annotation t1 : tt) {
                    if (t1 instanceof MyAnTargetParameter) {
                        System.out.println("参数上的注解值 === " + ((MyAnTargetParameter) t1).value());
                    }
                }
            }
            method.invoke(new AnnotationTest(), "改变默认参数");
            // 获取AnnotationTest类上字段field的注解MyAnTargetField
            MyAnTargetField fieldAn = AnnotationTest.class.getDeclaredField("field").getAnnotation(MyAnTargetField.class);
            System.out.println("字段上的注解值 === " + fieldAn.value());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}
```

输出结果

```java
类上的注解值 === 定义在类接口枚举类上的注解元素value的默认值
方法上的注解值 === 测试方法
参数上的注解值 === 定义在参数上的注解元素value的默认值
参数值 === 改变默认参数
字段上的注解值 === 定义在字段上的注解元素value的默认值
```



## Java预置的注解

学习了上面相关的知识，我们已经可以自己定义一个注解了。其实 Java 语言本身已经提供了几个现成的注解。

### @Deprecated

这个元素是用来标记过时的元素，想必大家在日常开发中经常碰到。编译器在编译阶段遇到这个注解时会发出提醒警告，告诉开发者正在调用一个过时的元素比如过时的方法、过时的类、过时的成员变量。

### @Override

这个大家应该很熟悉了，用于标明此方法覆盖了父类的方法。

### @SuppressWarnings

用于有选择的关闭编译器对类、方法、成员变量、变量初始化的警告。之前说过调用被 @Deprecated 注解的方法后，编译器会警告提醒，而有时候开发者会忽略这种警告，他们可以在调用的地方通过 @SuppressWarnings 达到目的。

### @SafeVarargs

参数安全类型注解。它的目的是提醒开发者不要用参数做一些不安全的操作，它的存在会阻止编译器产生 unchecked 这样的警告。它是在 Java 1.7 的版本中加入的。

### @FunctionalInterface

函数式接口注解，这个是 Java 1.8 版本引入的新特性。函数式编程很火，所以 Java 8 也及时添加了这个特性。

函数式接口 (Functional Interface) 就是一个具有一个方法的普通接口。

我们进行线程开发中常用的 Runnable 就是一个典型的函数式接口，从源码可以看到它使用了@FunctionalInterface 注解。



## 注解应用实例

注解的功能很强大，Spring和Hebernate这些框架在日志和有效性中大量使用了注解功能。

注解通常配合反射或者切面一起使用来实现相应的业务逻辑。

我这里向大家演示使用注解和切面进行日志记录的案例

定义一个系统日志注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface SysLog {

    /**
     * 操作说明
     */
    String value() default "";

}
```

找到使用了注解的方法，利用切面进行日志记录

这里有些类或者参数只做演示，没有给出具体的实现（如SysLogMapper、SysLogEntity等），以看懂注解配合切面使用为目的

```java
@Aspect
@Component
public class SysLogAspect {

    @Autowired
    private SysLogMapper sysLogMapper;

    @Pointcut("@annotation(com.jourwon.annotation.SysLog)")
    public void logPointCut() {

    }

    @Around("logPointCut()")
    public Object around(ProceedingJoinPoint point) throws Throwable {
        long beginTime = System.currentTimeMillis();
        //执行方法
        Object result = point.proceed();
        //执行时长(毫秒)
        long time = System.currentTimeMillis() - beginTime;

        //保存日志
        saveSysLog(point, time);

        return result;
    }

    private void saveSysLog(ProceedingJoinPoint joinPoint, long time) {
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        Method method = signature.getMethod();

        SysLogEntity sysLog = new SysLogEntity();
        SysLog syslog = method.getAnnotation(SysLog.class);
        if (syslog != null) {
            //注解上的描述
            sysLog.setOperation(syslog.value());
        }

        //保存系统日志
        sysLogMapper.insertSelective(sysLog);
    }

}
```

在方法中使用注解

```java
@SysLog("保存用户")
@PostMapping("/save")
public Result save(@RequestBody SysUserEntity user) {
    sysUserService.save(user);

    return Result.ok();
}
```



## 总结

本文首先讲述了为什么要引入注解，注解是什么，注解的用途和分类，了解什么是元注解，怎么自定义注解，最后演示了使用注解和切面进行日志记录的案例。本人知识水平有限，只进行了Java注解的简单介绍，如有不正确的地方还请大神指教，希望对注解有更深入的了解掌握的话，可以自行进行更深入的学习。