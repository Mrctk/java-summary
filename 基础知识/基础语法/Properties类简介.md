# Properties类简介

[TOC]

## 概述

Properties 继承于 Hashtable。表示一个持久的属性集，属性列表以key-value的形式存在，key和value都是字符串。

Java中有个比较重要的类Properties（Java.util.Properties），主要用于读取Java的配置文件，各种语言都有自己所支持的配置文件，配置文件中很多变量是经常改变的，这样做也是为了方便用户，让用户能够脱离程序本身去修改相关的变量设置。像Python支持的配置文件是.ini文件，同样，它也有自己读取配置文件的类ConfigParse，方便程序员或用户通过该类的方法来修改.ini配置文件。在Java中，其配置文件常为.properties文件，格式为文本文件，文件的内容的格式是“键=值”的格式，文本注释信息可以用"#"来注释。



## 常用方法

除了从Hashtable中所定义的方法，Properties定义了以下方法：

| 返回值类型       | 方法和描述                                                   |
| ---------------- | ------------------------------------------------------------ |
| `String`         | `getProperty(String key)`  搜索具有此属性列表中的指定键属性。 |
| `String`         | `getProperty(String key, String defaultValue)`  搜索具有此属性列表中的指定键属性。 |
| `void`           | `list(PrintStream out)`  将此属性列表输出到指定的输出流。    |
| `void`           | `list(PrintWriter out)`  将此属性列表输出到指定的输出流。    |
| `void`           | `load(InputStream inStream)`  从输入的字节流中读取属性列表 （键和元素对）。 |
| `void`           | `load(Reader reader)`  属性列表 （键和元素对） 从流中读取的输入的字符在一个简单的面向行的格式。 |
| `void`           | `loadFromXML(InputStream in)`  加载所有到此属性表所指定的输入流的 XML 文档表示的属性。 |
| `Enumeration<?>` | `propertyNames()`   在此属性列表中，如果具有相同名称的密钥不已发现从主要属性列表中的默认属性列表中包括非重复键返回所有键的枚举。 |
| `void`           | `save(OutputStream out, String comments)`  弃用。  如果发生 I/O  错误，则保存在属性列表中，此方法不会引发时抛出。保存的属性列表的首选的方法是通过 `store(OutputStream out, String  comments)` 方法或 `storeToXML(OutputStream os, String comment)`  方法。 |
| `Object`         | `setProperty(String key, String value)`  调用 `Hashtable` 方法 `put`。 |
| `void`           | `store(OutputStream out, String comments)`  此属性列表 （键和元素对） 此 `Properties` 表中写入输出流中适合装载到  `Properties` 表中使用 [`load(InputStream)`](../../java/util/Properties.html#load-java.io.InputStream-)  方法的格式。 |
| `void`           | `store(Writer writer, String comments)`  此属性列表 （键和元素对） 此 `Properties` 表中写入输出字符流格式适合使用 [`load(Reader)`](../../java/util/Properties.html#load-java.io.Reader-)  方法。 |
| `void`           | `storeToXML(OutputStream os, String comment)`  发出代表所有包含此表中的属性的 XML 文档。 |
| `void`           | `storeToXML(OutputStream os, String comment, String encoding)`  发出代表所有包含在此表中，使用指定的编码的属性的 XML 文档。 |
| `Set<String>`    | `stringPropertyNames()`   返回一组键此属性列表中的关键和其对应的值都是字符串，默认属性列表中包括非重复键，如果具有相同名称的密钥不已发现从主要属性列表。 |



## 常用方法实践

Properties类

下面我们从**写入、读取、遍历**等角度来解析Properties类的常见用法

项目路径如下

```java
src
	main
		java
			com
				jourwon
					prop
						PropertiesTest
		resources
			config.properties
			prop.properties
```

PropertiesTest为测试类，prop.properties文件的内容如下

```properties
username=root
password=123456
```



### 写入

Properties类调用setProperty方法将键值对保存到内存中，此时可以通过getProperty方法读取，propertyNames方法进行遍历，但是并没有将键值对持久化到属性文件中，故需要调用store方法持久化键值对到属性文件中。

```java
public static void main(String[] args) throws IOException {
    Properties properties = new Properties();
    OutputStream output = null;
    try {
        output = new FileOutputStream("src/main/resources/config.properties");
        properties.setProperty("username", "root");
        properties.setProperty("password", "123456");

        // 保存键值对到文件中
        properties.store(output, "JourWon modify");

    } catch (IOException io) {
        io.printStackTrace();
    } finally {
        if (output != null) {
            try {
                output.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

输出结果，在resources目录下多一个文件config.properties，内容如下

```properties
#JourWon modify
#Mon Sep 09 14:23:44 CST 2019
password=123456
username=root
```



### 读取

下面给出常见的六种读取properties文件的方式：

```java
public static void main(String[] args) throws IOException {
    // PropertiesTest.getPath1();
    // PropertiesTest.getPath2();
    // PropertiesTest.getPath3();
    // PropertiesTest.getPath4();
    // PropertiesTest.getPath5();
    PropertiesTest.getPath6();
}

/**
     * 一、 使用java.util.Properties类的load(InputStream in)方法加载properties文件
     * 主要是需要加上src这个文件夹名。路径配置需要精确到绝对地址级别
     *
     * @return
     */
public static void getPath1() throws IOException {
    InputStream in = new BufferedInputStream(new FileInputStream(
        new File("src/main/resources/prop.properties")));

    printKeyValue(in);

}

/**
     * 二、 使用java.util.ResourceBundle类的getBundle()方法
     * 注意：这个getBundle()方法的参数只能写成包路径+properties文件名，注意不需要带上后缀名。
     *
     * @return
     */
public static void getPath2() {
    ResourceBundle rb = ResourceBundle
        .getBundle("prop");

    printKeyValueRb(rb);
}

/**
     * 三、 使用java.util.PropertyResourceBundle类的构造函数
     *
     * @return
     */
public static void getPath3() throws IOException {
    InputStream in = new BufferedInputStream(new FileInputStream("src/main/resources/prop.properties"));
    ResourceBundle rb = new PropertyResourceBundle(in);

    printKeyValueRb(rb);
}

/**
     * 四、 使用class变量的getResourceAsStream()方法
     * 注意：getResourceAsStream()方法的参数按格式写到包路径+properties文件名+.后缀
     *
     * @return
     */
public static void getPath4() throws IOException {
    InputStream in = PropertiesTest.class
        .getResourceAsStream("/prop.properties");

    printKeyValue(in);
}

/**
     * 五、
     * 使用class.getClassLoader()所得到的java.lang.ClassLoader的
     * getResourceAsStream()方法
     * getResourceAsStream(name)方法的参数必须是包路径+文件名+.后缀
     * 否则会报空指针异常
     *
     * @return
     */
public static void getPath5() throws IOException {
    InputStream in = PropertiesTest.class.getClassLoader()
        .getResourceAsStream("./././prop.properties");

    printKeyValue(in);
}

/**
     * 六、 使用java.lang.ClassLoader类的getSystemResourceAsStream()静态方法
     * getSystemResourceAsStream()方法的参数格式也是有固定要求的
     *
     * @return
     */
public static void getPath6() throws IOException {
    InputStream in = ClassLoader
        .getSystemResourceAsStream("./././prop.properties");

    printKeyValue(in);
}


/**
     * 单独抽取的方法，用户检测能否正确操纵Properties
     *
     * @param inputStream
     * @throws IOException
     */
public static void printKeyValue(InputStream inputStream) throws IOException {
    Properties properties = new Properties();
    properties.load(inputStream);
    Set<Object> keys = properties.keySet();
    for (Object key : keys) {
        System.out.println(key + " = " + properties.get(key));
    }

    if (inputStream != null) {
        inputStream.close();
    }
}

public static void printKeyValueRb(ResourceBundle rb) {
    Set<String> keys = rb.keySet();
    for (String key : keys) {
        System.out.println(key + " = " + rb.getString(key));

    }
}
```

输出结果都是

```properties
password = 123456
username = root
```



其中第一、四、五、六种方式都是先获得文件的输入流，然后通过Properties类的load(InputStream inStream)方法加载到Properties对象中，最后通过Properties对象来操作文件内容。
第二、三中方式是通过ResourceBundle类来加载Properties文件，然后ResourceBundle对象来操做properties文件内容。
**其中最重要的就是每种方式加载文件时，文件的路径需要按照方法的定义的格式来加载，否则会抛出各种异常，比如空指针异常。**



### 遍历

下面给出四种遍历Properties中的所有键值对的方法：

```java
/**
  * 输出properties的key和value
  */
public static void printProp(Properties properties) {
    System.out.println("---------（方式一）------------");
    for (String key : properties.stringPropertyNames()) {
        System.out.println(key + "=" + properties.getProperty(key));
    }

    System.out.println("---------（方式二）------------");
    //返回属性key的集合
    Set<Object> keys = properties.keySet();
    for (Object key : keys) {
        System.out.println(key.toString() + "=" + properties.get(key));
    }

    System.out.println("---------（方式三）------------");
    Set<Map.Entry<Object, Object>> entrySet = properties.entrySet();
    //返回的属性键值对实体
    for (Map.Entry<Object, Object> entry : entrySet) {
        System.out.println(entry.getKey() + "=" + entry.getValue());
    }

    System.out.println("---------（方式四）------------");
    Enumeration<?> e = properties.propertyNames();
    while (e.hasMoreElements()) {
        String key = (String) e.nextElement();
        String value = properties.getProperty(key);
        System.out.println(key + "=" + value);
    }
}
```