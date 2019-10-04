# XML简介

![xml](https://raw.githubusercontent.com/JourWon/image/master/XML简介/xml.jpg)

[TOC]

## 什么是 XML

- XML 是可扩展标记语言（EXtensible Markup Language）。
- XML 是一种很像HTML的标记语言。
- XML 的设计宗旨是传输数据，而不是显示数据。
- XML 标签没有被预定义。您需要自行定义标签。
- XML 被设计为具有自我描述性。
- XML 是 W3C 的推荐标准。



## XML 用途
XML 应用于 Web 开发的许多方面，常用于简化数据的存储和传输。

存放数据栗子

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persons>
    <person id="p001">
        <name>张三</name>
    </person>
    <person id="p002">
        <name>李四</name>
    </person>
</persons>
```

配置文件栗子

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5">
    <servlet>
        <servlet-name>HelloMyServlet</servlet-name>
        <servlet-class>com.jourwon.HelloMyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloMyServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```



## XML 语法

XML文件主要由XML文档声明，元素，属性，注释，转义字符，CDATA区，处理指令组成。

### XML文档声明

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

1. 文档声明必须为`<?xml开头，以?>结束`；
2. 文档声明必须从文档的0行0列位置开始；
3. 文档声明只有三个属性：

- versioin：指定XML文档版本。必须属性，因为我们不会选择1.1，只会选择1.0；
- encoding：指定当前文档的编码。可选属性，默认值是utf-8；
- standalone：指定文档独立性。可选属性，默认值为yes，表示当前文档是独立文档。如果为no表示当前文档不是独立的文档，会依赖外部文件。



### 元素

```xml
<servlet>
```

1. 元素是XML文档中最重要的组成部分，
2. 普通元素的结构开始标签、元素体、结束标签组成。例如：<hello>大家好</hello>
3. 元素体：元素体可以是元素，也可以是文本，例如：<b><a>你好</a></b>
4. 空元素：空元素只有开始标签，而没有结束标签，但元素必须自己闭合，例如：<c/>
5. 元素命名：
   a) 区分大小写
   b) 不能使用空格，不能使用冒号:
   c) 不建议以XML、xml、Xml开头
6. 良好的XML文档，必须有一个根元素。



### 属性

```xml
<web-app version="2.5">
```

1. 属性是元素的一部分，它必须出现在元素的开始标签中
2. 属性的定义格式：属性名=属性值，其中属性值必须使用单引或双引
3. 一个元素可以有0~N个属性，但一个元素中不能出现同名属性
4. 属性名不能使用空格、冒号等特殊字符，且必须以字母开头



### 注释

1. XML 文件中注释采用："<!-- 注释 -->" 这样的格式

2. XML 声明之前不能有注释

3. 注释不能嵌套，比如下面不合规范：

   <!-- 全局注释   <!-- 局部注释 -->-->



### 转义字符

XML中的转义字符与HTML一样。
因为很多符号已经被XML文档结构所使用，所以在元素体或属性值中想使用这些符号就必须使用转义字符，例如：“<”、“>”、“’”、“””、“&”。

| 转义字符 | 特殊字符 | 含义   |
| -------- | -------- | ------ |
| \&lt;    | <        | 小于   |
| \&gt;    | >        | 大于   |
| \&amp;   | &        | 和号   |
| \&apos;  | '        | 单引号 |
| \&quot;  | "        | 引号   |

 

### CDATA区

```xml
<![CDATA[
    任意内容
]]>
```

当大量的转义字符出现在xml文档中时，会使xml文档的可读性大幅度降低。这时如果使用CDATA段就会好一些。
在CDATA段中出现的“<”、“>”、“””、“’”、“&”，都无需使用转义字符。这可以提高xml文档的可读性。
在CDATA段中不能包含“]]>”，即CDATA段的结束定界符。



### 处理指令

用来解析引擎如何解析 XML 文档内容

比如：在 XML 文档中可以使用 xml-stylesheet 指令，通知 XML 解析引擎，应用 CSS 文件显示 XML 文档内容

<?xml-stylesheet type="text/css" href="a.css"?>

处理指令必须以<? 开头，以 > 结尾



## XML的解析

### 开发中比较常见的解析方式有三种

- DOM：要求解析器把整个XML文档装载到内存，并解析成一个Document对象。
  a) 优点：元素与元素之间保留结构关系，故可以进行增删改查操作。
  b) 缺点：XML文档过大，可能出现内存溢出显现。
- SAX：是一种速度更快，更有效的方法。它逐行扫描文档，一边扫描一边解析。并以事件驱动的方式进行具体解析，每执行一行，都将触发对应的事件。（了解）
  a) 优点：处理速度快，可以处理大文件
  b) 缺点：只能读，逐行后将释放资源。
- PULL：Android内置的XML解析方式，类似SAX。（了解）



### 解析器

根据不同的解析方式提供的具体实现。有的解析器操作过于繁琐，为了方便开发人员，有提供易于操作的解析开发包。



### 常见的解析开发包

- JAXP：sun公司提供支持DOM和SAX开发包
- JDom：dom4j兄弟
- jsoup：一种处理HTML特定解析开发包
- dom4j：比较常用的解析开发包，hibernate底层采用。



### DOM解析原理及结构模型

XML DOM 和 HTML DOM类似，XML DOM 将 整个XML文档加载到内存，生成一个DOM树，并获得一个Document对象，通过Document对象就可以对DOM进行操作

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5">
    <servlet>
        <servlet-name>HelloMyServlet</servlet-name>
        <servlet-class>com.jourwon.HelloMyServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloMyServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

![DOM解析](https://raw.githubusercontent.com/JourWon/image/master/XML简介/DOM解析.png)

DOM中的核心概念就是节点，在XML文档中的元素、属性、文本等，在DOM中都是节点！



### dom4j技术栗子

引入dom4j依赖

```xml
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.1</version>
</dependency>
```



将xml文件放到指定位置，这里放的是XML 用途配置文件栗子的内容，然后读取这个位置的xml文件，测试代码如下

```cpp
public class Dome4jTest {

    public static void main(String[] args) throws DocumentException {
        // 1 创建核心对象（new 方式）
        SAXReader saxReader = new SAXReader();
        //2 加载xml文档 获得dom树（核心对象调用read读取xml文件）
        Document doc = saxReader.read("E:\\IdeaProjects\\demo1\\src\\main\\resources\\demo.xml");
        //3 获得根元素（文档对象下面就是根元素）
        Element root = doc.getRootElement();
        //4 获得子元素（根节点下所有子元素）
        List<Element> list = root.elements();

        for (Element e : list) {
            // elementText("标签名") 获得指定名称元素的文本值 （指定标签的文本值）
            // getName() 获得当前元素名
            if ("servlet".equals(e.getName())) {
                System.out.println(e.elementText("servlet-name"));
                System.out.println(e.getName());
            }

        }
    }

}
```

输出结果

```java
HelloMyServlet
servlet
```



## XML的约束

### 作用

规定xml中可以出现哪些元素及哪些属性，以及他们出现的顺序

### 约束的分类

- DTD约束:struts hiebernate等等（会用到）
- SCHEMA约束:tomcat spring等等（会用到）



### 约束详解

既然是约束就和xml有联系，所以约束要和xml关联，xml的相关限制才能生效。



#### 方式1:内部关联

```xml
格式:
<!DOCTYPE 根元素名 [dtd语法]>
```

#### 方式2:外部关联-系统关联

```xml
格式:
<!DOCTYPE 根元素名 SYSTEM "约束文件的位置">
例如:
<!DOCTYPE web-app SYSTEM "web-app_2_3.dtd">
```

举例web-app_2_3.dtd文件

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!ELEMENT web-app (servlet*, servlet-mapping* , welcome-file-list?) >
<!ELEMENT servlet (servlet-name, description?, (servlet-class|jsp-file))> 
<!ELEMENT servlet-mapping (servlet-name, url-pattern) >
<!ELEMENT servlet-name (#PCDATA)>
<!ELEMENT servlet-class (#PCDATA)>
<!ELEMENT url-pattern (#PCDATA)>
<!ELEMENT welcome-file-list (we1come-fi1e+)> 
<!ELEMENT welcome-file (#PCDATA)>
<!ATTLIST web-app version CDATA #IMPLIED>
```

接下来xml中只能出现dtd文件中规定的特有的标签了（不能自定义自己随便写了）

ps：idea中你输入< 便会提示能够输入的标签



#### 方式3:外部关联-公共关联

```xml
格式:
<!DOCTYPE 根元素名 PUBLIC "约束文件的名称" "约束文件的位置">
如mybatis中mapper的约束
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
```



### dtd约束

无论是单独的dtd文件还是写在xml中（参考上面方式一）都需要按照语法要求写

再次强调下语dtd文件约束的作用：

规定xml中可以出现哪些元素及哪些属性,以及他们出现的顺序

```text
标签（元素）语法：
<!Element 元素名称 数据类型|包含内容>

数据类型:#PCDATA:普通文本 使用的时候一般用()引起来

功能：能出现那些标签，以及出现顺序

包含内容:该元素下可以出现哪些元素 用()引起来

符号:
            *   出现任意次
            ?   出现1次或者0次
            +   出现至少1次
            |   或者
            ()  分组
            ,   顺序（元素出现先后顺序，按照都好标记顺序出现）

栗子如下：（上图约束图中的代码）
<!ELEMENT web-app (servlet*,servlet-mapping* , welcome-file-list?) >
<!ELEMENT servlet-name (#PCDATA)>

ps：元素名就是xml中能够出现的元素标签名
属相的约束语法：
                
    属性:
        格式:
            <!ATTLIST 元素名 属性名 属性类型 属性是否必须出现>
        属性类型:
            ID:唯一
            CDATA:普通文本
        属性是否必须出现
            REQUIRED:必须出现
            IMPLIED:可以不出现


例如：
<!ATTLIST web-app version CDATA #IMPLIED>
```

注意：一个xml文档中只能添加一个DTD约束



### schema约束

一个xml文档中可以添加多个schema约束

```jsx
xml和schema的关联.
        格式:
            <根标签 xmlns="..." ...>
            <根标签 xmlns:别名="..." ...>
    名称空间:
        关联约束文件
        规定元素是来源于那个约束文件的
    例如:
        一个约束文件中规定 table(表格)  表格有属性 row和col
        还有一个约束文件规定 table(桌子) 桌子有属性 width和height
        
        在同一个xml中万一我把两个约束文件都导入了,
            在xml中我写一个table,这个table有什么属性????
        我们为了避免这种情况的发生,可以给其中的一个约束起个别名
        使用的时候若是没有加别名那就代表是来自于没有别名的约束文件
            例如 table(表格) 给他起个别名  xmlns:a="..."
            在案例中使用 a:table 代表的是表格
            若在案例中直接使用 table 代表的是桌子
        
     ps:在一个xml文件中只能有一个不起别名;
注意:
    schema约束本身也是xml文件.
```

如下一个xml scheam的约束文档，我们创建xml时如果有相应的约束 直接引进约束就行。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 
    
    模拟servlet2.5规范，如果开发人员需要在xml使用当前Schema约束，必须包括指定命名空间。
    格式如下：
    <web-app xmlns="http://www.example.org/web-app_2_5" 
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.example.org/web-app_2_5 web-app_2_5.xsd"
            version="2.5">
-->
<xsd:schema xmlns="http://www.w3.org/2001/XMLSchema" 
    targetNamespace="http://www.example.org/web-app_2_5"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns:tns="http://www.example.org/web-app_2_5" 
    elementFormDefault="qualified">
    
    <xsd:element name="web-app">
        <xsd:complexType>
            <xsd:choice minOccurs="0" maxOccurs="unbounded">
                <xsd:element name="servlet">
                    <xsd:complexType>
                        <xsd:sequence>
                            <xsd:element name="servlet-name"></xsd:element>
                            <xsd:element name="servlet-class"></xsd:element>
                        </xsd:sequence>
                    </xsd:complexType>
                </xsd:element>
                <xsd:element name="servlet-mapping">
                    <xsd:complexType>
                        <xsd:sequence>
                            <xsd:element name="servlet-name"></xsd:element>
                            <xsd:element name="url-pattern"></xsd:element>
                        </xsd:sequence>
                    </xsd:complexType>
                </xsd:element>
                <xsd:element name="welcome-file-list">
                    <xsd:complexType>
                        <xsd:sequence>
                            <xsd:element name="welcome-file" maxOccurs="unbounded"></xsd:element>
                        </xsd:sequence>
                    </xsd:complexType>
                </xsd:element>
            </xsd:choice>
            <xsd:attribute name="version" type="double" use="optional"></xsd:attribute>
        </xsd:complexType>
    </xsd:element>
</xsd:schema>
```
