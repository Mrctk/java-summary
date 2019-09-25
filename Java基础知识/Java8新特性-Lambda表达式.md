# Java8新特性-Lambda表达式

[TOC]

## 简介

Lambda表达式（也称**闭包**），是Java8中最受期待和欢迎的新特性之一。在Java语法层面Lambda表达式允许函数作为一个方法的参数（函数作为参数传递到方法中），或者把代码看成数据。Lambda表达式可以简化函数式接口的使用。**函数式接口就是一个只具有一个抽象方法的普通接口**，像这样的接口就可以使用Lambda表达式来简化代码的编写。



## 使用Lambda表达式的前提

**对应接口有且只有一个抽象方法！！！**



## 基础语法

Lambda 表达式的基础语法：Java8中引入了一个新的操作符 "->" 该操作符称为箭头操作符或 Lambda 操作符
箭头操作符将 Lambda 表达式拆分成两部分：
左侧：Lambda 表达式的参数列表
右侧：Lambda 表达式中所需执行的功能， 即 Lambda 体

```java
(args1, args2...) -> {};
```



## Lambda表达式的重要特征

**可选类型声明**：不需要声明参数类型，编译器可以统一识别参数值。
**可选的参数圆括号**：一个参数无需定义圆括号，但多个参数需要定义圆括号。
**可选的大括号**：如果主体包含了一个语句，就不需要使用大括号。
**可选的返回关键字**：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。



## 使用Lambda表达式的优缺点

**优点**

使用Lambda表达式可以简化接口匿名内部类的使用，可以减少类文件的生成，可能是未来编程的一种趋势。

**缺点**

使用Lambda表达式会减弱代码的可读性，而且Lambda表达式的使用局限性比较强，只能适用于接口只有一个抽象方法时使用，不宜调试。



## 函数式接口

- **只有函数式接口，才可以转换为lambda表达式**
- **有且只有一个抽象方法的接口被成为函数式接口！**
- **函数式接口可以显式的被@FunctionalInterface所表示，当被标识的接口不满足规定时，编译器会提示报错**



## 案例1 无参无返回

```java
public class Demo01 {

    public static void main(String[] args) {

        // 1.传统方式 需要new接口的实现类来完成对接口的调用
        ICar car1 = new IcarImpl();
        car1.drive();

        // 2.匿名内部类使用
        ICar car2 = new ICar() {
            @Override
            public void drive() {
                System.out.println("Drive BMW");
            }
        };
        car2.drive();

        // 3.无参无返回Lambda表达式
        ICar car3 = () -> {System.out.println("Drive Audi");};
        car3.drive();

        // 4.无参无返回且只有一行实现时可以去掉{}让Lambda更简洁
        ICar car4 = () -> System.out.println("Drive Ferrari");
        car4.drive();

        // 去查看编译后的class文件 大家可以发现 使用传统方式或匿名内部类都会生成额外的class文件，而Lambda不会
    }

}

interface ICar {

    void drive();
}

class IcarImpl implements ICar {

    @Override
    public void drive() {
        System.out.println("Drive Benz");
    }
}
```



## 案例2 有参有返回值

```java
public class Demo02 {

    public static void main(String[] args) {

        // 1.有参无返回
        IEat eat1 = (String thing) -> System.out.println("eat " + thing);
        eat1.eat("apple");

        // 参数数据类型可以省略
        IEat eat2 = (thing) -> System.out.println("eat " + thing);
        eat2.eat("banana");

        // 2.多个参数
        ISpeak speak1 = (who, content) -> System.out.println(who + " talk " + content);
        speak1.talk("John", "hello word");

        // 3.返回值
        IRun run1 = () -> {
            return 10;
        };
        run1.run();

        // 4.返回值简写
        IRun run2 = () -> 10;
        run2.run();
    }

}

interface IEat {

    void eat(String thing);
}

interface ISpeak {

    void talk(String who, String content);
}

interface IRun {

    int run();
}
```



## 案例3 final类型参数

```java
public class Demo03 {

    public static void main(String[] args) {

        // 全写
        IAddition addition1 = (final int a, final int b) -> a + b;
        System.out.println(addition1.add(1, 2));
        // 简写
        IAddition addition2 = (a, b) -> a+b;
        System.out.println(addition2.add(2, 3));
    }

}
interface IAddition {

    int add(final int a, final int b);
}
```



## Java8内置的函数式接口

Java8提供了一个java.util.function包，包含了很多函数式接口，我们来介绍最为基本的4个（为了节省篇幅，去掉了源码中的注释）

### Function接口

```java
@FunctionalInterface
public interface Function<T, R> {

    R apply(T t);

    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }

    static <T> Function<T, T> identity() {
        return t -> t;
    }
}
```



Function接口的唯一抽象方法是apply，作用是接收一个指定类型的参数，返回一个指定类型的结果

```java
public class FunctionTest1 {

    public static void main(String[] args) {
        FunctionTest1 ft = new FunctionTest1();
        //使用lambda表达式实现apply方法，返回入参+10。形式上如同传递了一个方法作为参数
        int res = ft.compute(1, v -> v + 10);
        System.out.println(res);//11
    }

    public int compute(int a, Function<Integer, Integer> function) {
        //使用者在使用本方法时，需要去编写自己的apply，
        //传递的funtion是一个行为方法，而不是一个值
        return function.apply(a);
    }

}
```



默认方法compose作用是传入参数后，首先执行compose方法内的Function的apply方法，然后将其返回值作为本Function方法的入参，调用apply后得到最后返回值

```java
public class FunctionTest2 {

    public static void main(String[] args) {
        FunctionTest2 ft = new FunctionTest2();
        //调用compose
        //先+8，然后将得到的值*3
        System.out.println(ft.compute(2, v -> v * 3, v -> v + 8));//30
    }

    public int compute(int a, Function<Integer, Integer> function1, Function<Integer, Integer> function2) {
        //将function2先接收入参a，调用apply后，将返回值作为新的入参，传入function1，调用apply返回最后结果
        return function1.compose(function2).apply(a);
    }

}
```



默认方法andThen与compose正好相反，先执行本Function的apply，然后将结果作为andThen方法参数内的Function的入参，调用apply后返回最后结果

```java
public class FunctionTest3 {

    public static void main(String[] args) {
        FunctionTest3 ft = new FunctionTest3();
        //调用andThen
        //先*3，然后将得到的值+8
        System.out.println(ft.compute(2, v -> v * 3, v -> v + 8));//14
    }

    public int compute(int a, Function<Integer, Integer> function1, Function<Integer, Integer> function2) {
        //将function2先接收入参a，调用apply后，将返回值作为新的入参，传入function1，调用apply返回最后结果
        return function1.andThen(function2).apply(a);
    }

}
```

静态方法identity的作用是传入啥返回啥，这里就不写例子了



### Consumer接口

```java
package java.util.function;

import java.util.Objects;
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

Consumer接口中accept方法的作用是接收指定参数类型，无返回值，重点在于内部消费

```java
Consumer<String> consumer = s -> System.out.println("hello " + s);
consumer.accept("mike");// hello mike
```

默认方法andThen作用是连续消费，从本Consumer开始，从外到内，针对同一入参。

```java
Consumer<String> consumer = s -> System.out.println("hello " + s);
Consumer<String> consumer2 = s -> System.out.println("nice to meet you " + s);
consumer.andThen(consumer2).accept("mike");
//hello mike
//nice to meet you mike
```



### Predicate接口

```java
package java.util.function;

import java.util.Objects;
@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);

    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }

    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
}
```

Predicate中的test方法，传入指定类型参数，返回布尔类型的结果，用于判断，断言

```java
//判断一个数是否是偶数
Predicate<Integer> predicate = b -> n % 2 == 0;
System.out.println(predicate.test(3));//false
```

默认方法and顾名思义，将本Predicate和and参数中的Predicate对同一入参进行test的结果进行【与】操作。
negate方法对test的结果进行【非】操作
or方法对两个Predicate的test结果进行【或】操作

静态方法isEqual将其入参与test方法的入参进行equals比较

```java
System.out.println(Predicate.isEqual(1).test(1));//true
```



### Supplier接口

```java
package java.util.function;
@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

Supplier意为供应，只有一个方法get，不接收任何参数，只返回指定类型结果

```java
Supplier<String> sup = () -> "hello world";
System.out.println(sup.get());
```

