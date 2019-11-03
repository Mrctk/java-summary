# Eclipse & Spring Tool Suite常用配置

Eclipse 是一个开放源代码的、基于Java的可扩展开发平台。Eclipse 是Java开发人员常用的开发工具，虽然现在很多人转向使用IDEA，但是Eclipse给我们还是留下了一段美好的回忆。

在基于Eclipse的基础上，Spring提供了针对Spring开发的Spring Tool Suite集成开发工具，通过该工具，可以很轻易地生成一个Spring的工程，比如web工程，最令人兴奋的是工程里的配置文件都会自动为你生成，你再也不用操心配置文件的格式及各种配置文件了。

Spring Tool Suite的官方下载地址是：[https://spring.io/tools](https://spring.io/tools)

本博客以Spring Tool Suite4为基础，分享自己开发中的常用配置。



[TOC]

## 配置jdk

默认情况下，MyEclipse会自动关联环境变量中配置的JDK，如果我们安装了多个版本的JDK，也可以手工进行配置，方法如下： “Window”–“Preferences”–“Java”–“Installed JREs”–“Add”–“Standard VM”–选择jdk安装目录。

![配置jdk](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/配置jdk.png)

 

## 修改编码格式

在菜单导航栏上Window–>Preferences 打开”首选项”对话框，左侧导航树，导航到 General–>Workspace。Windows 7和10平台默认为GBK，简体中文操作系统Windows XP、Windows 2000简体中文的缺省编码是GB18030，Linux平台默认为UTF-8.

那么在此工作空间中建立的工程编码是GBK，工程中建立的java文件也是GBK。 

可以看到默认的是GBK编码格式,我们修改为UTF-8编码格式或者是你需要的编码格式 

点击Apply,点击OK

![修改编码格式](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/修改编码格式.png)

 

## 修改属性文件的编码

在MyEclipse项目文件上右键,选择Properties,在Resource选择修改编码格式

![修改属性文件的编码](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/修改属性文件的编码.png)

 

## 修改新建jsp编码格式

MyEclipse中新建jsp文件的编码格式。windows -> preferences -> web -> jsp files把右边encoding的值改为ISO 10646/Unicode(UTF-8),然后应用。ok，此后新建jsp文件的内容就是UTF-8编码的了。

![修改新建jsp编码格式](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/修改新建jsp编码格式.png)

 

## 设置代码的字体类型和大小

Window -> Preferences -> General -> Appearance -> Content Assist -> Colors and Fornts，只需修改 Basic 里面的 Text Font 就可以了

![设置代码的字体类型和大小](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置代码的字体类型和大小.png)

 

## 设置新建菜单项

MyEclipse默认的新建内容并不满足需求，好多内容还需要到other中去找，不过我们可以自定义新建菜单项中的内容，方法如下： Window–>Preferences–“Customize Prespective…”–“Shortcuts”–选择你需要的新建项即可。

![设置新建菜单项1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置新建菜单项1.png)

![img](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置新建菜单项2.png)



## 安装maven插件

首先安装好maven插件，然后配置maven仓库

![安装maven插件1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/安装maven插件1.png)

![安装maven插件2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/安装maven插件2.png)

 

## 安装svn插件

在help -> Install New Software

![安装svn插件1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/安装svn插件1.png)

![安装svn插件2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/安装svn插件2.png)

然后一路next，重启STS后可以找到SVN资源库



## 安装阿里p3c插件

插件安装

环境：JDK1.8，Eclipse4+。有同学遇到过这样的情况，安装插件重启后，发现没有对应的菜单项，从日志上也看不到相关的异常信息，最后把JDK从1.6升级到1.8解决问题。

Help -> Install New Software...

输入Update Site地址：https://p3c.alibaba.com/plugin/eclipse/update 回车，然后勾选Ali-CodeAnalysis，再一直点Next Next...按提示走下去就好。 然后就是提示重启了，安装完毕。

![安装阿里p3c插件](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/安装阿里p3c插件.png)

注意：有同学反映插件扫描会触发很多 "JPA Java Change Event Handler (Waiting)" 的任务，这个是Eclipse的一个bug，因为插件在扫描的时候会对文件进行标记，所以触发了JPA的任务。卸载JPA插件，或者尝试升级到最新版的Eclipse。附：JPA project Change Event Handler问题解决



## 运行maven命令时报错

-Dmaven.multiModuleProjectDirectory system propery is not set

新建一个环境变量M2_HOME指向你的maven安装目录

M2_HOME=C:\develop\Maven\apache-maven-3.5.3

然后在Window->Preference->Java->Installed JREs->Edit

在Default VM arguments中设置

-Dmaven.multiModuleProjectDirectory=$M2_HOME

![运行maven命令时报错](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/运行maven命令时报错.png)

 

## 添加离线约束

有的时候，如果没有配置约束，在xml文件中就没有快捷提示，开发很不方便

DTD类型约束文件

1、window--->Preferences--->XML--->XML Catalog--->User Specified Entries窗口，点击Add按钮

2.在Add XML Catalog Entry 对话框中选择或输入以下内容: 

​        Location: C:\develop\Offline-Constraint-File\mybatis-3-config.dtd 

​        Key Type: URI 

​        KEY: http://mybatis.org/dtd/mybatis-3-config.dtd

 

XSD 类型约束文件

以配置dubbo约束为例

1.window--->Preferences--->XML--->XML Catalog--->User Specified Entries窗口，点击Add按钮

2.在Add XML Catalog Entry 对话框中选择或输入以下内容: 

​        Location: C:\develop\Offline-Constraint-File\ dubbo.xsd

​        Key Type: Schema Location 

​        KEY: http://code.alibabatech.com/schema/dubbo/dubbo.xsd

 

![添加离线约束](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/添加离线约束.png)

 

## 设置在创建新类时自动生成注释

windows-->preference 

Java-->Code Style-->Code Templates 

code-->new Java files

```java
${filecomment}
${package_declaration}

/**
 * Description: 
* 
 * @author JourWon
 * @date ${date} ${time}
 */
${typecomment}
${type_declaration}
```



![设置在创建新类时自动生成注释](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置在创建新类时自动生成注释.png)



## 给方法和重写方法生成注释

```java
/**
 * Description: 
 * @author JourWon
 * @date ${date} ${time}
 * ${tags}
 */
```



![给方法和重写方法生成注释](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/给方法和重写方法生成注释.png)

 

## 自动生成文档注释

打开注释模板编辑窗口：Window ->Preferences->java -> Code Style -> Code Template->Comments

```java
/**
 * Description: 
 * @author JourWon
 * @date ${date} ${time}
 * ${tags}
*/
```



![自动生成文档注释](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/自动生成文档注释.png)

 

## 关闭验证（可选）

默认MyEclipse会对workspace中的项目进行验证，验证的内容包括jsp内容、xml内容，等等，验证过程很消耗内存，所以建议关闭验证功能。关闭方法如下： “Window”–“Preferences”–“Validation”–“Disable All”。

![关闭验证](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/关闭验证.png)

 

## Spring Boot中读取属性配置文件出现中文乱码

Spring Tool Suite不需要进行此处理，只需要设置properties文件的编码为UTF-8

![Spring Boot中读取属性配置文件出现中文乱码1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Spring Boot中读取属性配置文件出现中文乱码1.png)

 

MyEclipse处理如下：

1.下载离线安装文件：

2.http://sourceforge.jp/projects/propedit/downloads/40156/jp.gr.java_conf.ussiy.app.propedit_5.3.3.zip/

3.安装与检验

将得到的文件直接解压，可得到这样一个文件夹 

jp.gr.java_conf.ussiy.app.propedit_5.3.3 ，直接将该文件夹复制到 Eclipse目录下覆盖插件文件夹。

4.重启MyEclipse。

5.选中 *.properties 文件，右键 - Open With ，你会看到多了一个 PropertiesEditor 子菜单。

6.将PropertiesEditor设为默认的打开方式

工具栏->Window->Preferences->General->Editors->File Associations,添加一个*.properties。

下方的 Associated editors 栏里有 PropertiesEditor 项，选中，点击 Default 按钮。

双击properties文件默认就会用PropEditor打开了。

![Spring Boot中读取属性配置文件出现中文乱码2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Spring Boot中读取属性配置文件出现中文乱码2.png)

![Spring Boot中读取属性配置文件出现中文乱码3](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Spring Boot中读取属性配置文件出现中文乱码3.png)

 

设置properties文件的编码为UTF-8

![Spring Boot中读取属性配置文件出现中文乱码4](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Spring Boot中读取属性配置文件出现中文乱码4.png)

 

![Spring Boot中读取属性配置文件出现中文乱码5](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Spring Boot中读取属性配置文件出现中文乱码5.png)

 

## 设置漂亮的Eclipse主题（Theme）（可选）

MyEclipse主题太丑？想设置护眼的主题？没问题

Help -> Eclipse Marketplace

![设置漂亮的Eclipse主题1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置漂亮的Eclipse主题1.png)

然后一路next，等待ide重启

![设置漂亮的Eclipse主题2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/设置漂亮的Eclipse主题2.png)



## 其他

### 修改pom.xml打开方式

Maven user interface ->open xml page

 

### 修改xml打开方式

General->editors->file associations->添加*.xml

![修改xml打开方式](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/修改xml打开方式.png)

 

### 使用Java视图（可选）

可以根据个人喜好使用不同的视图，个人喜欢用Java视图，因为下面那一栏比较长。

![使用Java视图1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/使用Java视图1.png)

![使用Java视图2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/使用Java视图2.png)



### 修改目录显示方式为Hierachical

![修改目录显示方式为Hierachical](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/修改目录显示方式为Hierachical.png)

 

### 过滤关闭的工程

![过滤关闭的工程1](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/过滤关闭的工程1.png)

![过滤关闭的工程2](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/过滤关闭的工程2.png)



### 通过工作集显示工程

![通过工作集显示工程](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/通过工作集显示工程.png)

 

### 关闭MyEclipse的自动更新（可选）

选择Install/Update——Automatic Updates，取消勾选右边的Automatically find new updates and notify me



### 如何迁移eclipse配置信息

将原工作空间配置文件：

​        metadata/.plugins/org.eclipse.core.runtime/.settings

拷贝覆盖到新工作空间配置文件即可：

​        metadata/.plugins/org.eclipse.core.runtime/.settings

 注：为避免出问题，迁移之前相关配置文件记得提前做备份



### Eclipse智能提示报错



解决方案：

1.点击链接 content assist

2.将Default Proposal Kinds 中 项目【Java Proposals(Code Recommenders)】前的√去掉

同时将【Java Proposals】和【Java Proposals(Task-Focused)】两个项目勾选上

![Eclipse智能提示报错](https://raw.githubusercontent.com/JourWon/image/master/Eclipse%26STS配置/Eclipse智能提示报错.png)