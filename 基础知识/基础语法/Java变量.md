# Java变量

[TOC]

## 定义

- 在程序执行的过程中，在某个范围内其值可以发生改变的量。
  从本质上讲，变量其实是内存中的一小块区域

  

## 分类

### 按被声明的位置划分

#### 成员变量

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

      

#### 局部变量

- 局部变量：类的方法中的变量。

- 局部变量声明在方法、构造方法或者语句块中；

- 局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁；

- 访问修饰符不能用于局部变量；

- 局部变量只在声明它的方法、构造方法或者语句块中可见；

- 局部变量是在栈上分配的。

- 局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。

  

#### 成员变量和局部变量的区别

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

    

#### 使用原则

- 在使用变量时需要遵循的原则为：就近原则 
  首先在局部范围找，有就使用；接着在成员位置找。

  

### 按所属的数据类型划分

#### 基本数据类型变量

- 基本数据类型
  - byte,short,int,long,float,double,char,boolean
- 变量的定义格式
  - 数据类型 变量名 = 初始化值;
- 注意
  - 整数默认是int类型，定义long类型的数据时，要在数据后面加L。
  - 浮点数默认是double类型，定义float类型的数据时，要在数据后面加F。

#### 引用数据类型变量



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