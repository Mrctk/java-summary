# IDEA常用配置和常用插件

> 本人使用 IntelliJ IDEA 近两年了，觉得还是非常好用的。在使用过程中总结了个人的IDEA常用配置和常用插件，觉得这样配置是有利于提高开发效率，特此分享给大家
>
> 注：本博客使用的IDEA版本是2018，不同版本的IDEA可能有些差异

[toc]



## 常用配置

### 配置JDK

在IDEA启动页面中，下拉Configure，选择Project Defaults -- Project Structure，这样可以设置所有项目的默认的JDK版本，如下图

![配置JDK](https://img-blog.csdnimg.cn/20190919134900196.png)

 

### 文件编码

![文件编码](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0lERUElRTUlQjglQjglRTclOTQlQTglRTklODUlOEQlRTclQkQlQUUlRTUlOTIlOEMlRTUlQjglQjglRTclOTQlQTglRTYlOEYlOTIlRTQlQkIlQjYvJUU2JTk2JTg3JUU0JUJCJUI2JUU3JUJDJTk2JUU3JUEwJTgxLnBuZw?x-oss-process=image/format,png)

 

### 文件和代码模板

![文件和代码模板](https://img-blog.csdnimg.cn/20190919134940157.png)

 

### Maven配置

![Maven配置](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0lERUElRTUlQjglQjglRTclOTQlQTglRTklODUlOEQlRTclQkQlQUUlRTUlOTIlOEMlRTUlQjglQjglRTclOTQlQTglRTYlOEYlOTIlRTQlQkIlQjYvTWF2ZW4lRTklODUlOEQlRTclQkQlQUUucG5n?x-oss-process=image/format,png)

 

### 自动导入Maven依赖

![自动导入Maven工程](https://img-blog.csdnimg.cn/20190919135012549.png)

 

### Java代码单行注释添加空格

![Java代码单行注释](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0lERUElRTUlQjglQjglRTclOTQlQTglRTklODUlOEQlRTclQkQlQUUlRTUlOTIlOEMlRTUlQjglQjglRTclOTQlQTglRTYlOEYlOTIlRTQlQkIlQjYvSmF2YSVFNCVCQiVBMyVFNyVBMCU4MSVFNSU4RCU5NSVFOCVBMSU4QyVFNiVCMyVBOCVFOSU4NyU4QS5wbmc?x-oss-process=image/format,png)



### 优化导入和智能删除无关依赖

![优化导入和智能删除无关依赖](https://img-blog.csdnimg.cn/20190919135155737.png)



### 修改主题

![修改主题](https://img-blog.csdnimg.cn/20190919135213154.png)



### 修改字体

![修改字体](https://img-blog.csdnimg.cn/20190919135228539.png)

 

### 代码提示不区分大小写

![代码提示不区分大小写](https://img-blog.csdnimg.cn/20190919135244518.png)

 

### 显示行数和方法线

![显示行数和方法线](https://img-blog.csdnimg.cn/20190919135259168.png)

 

### 目录展示设置

![目录展示](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0lERUElRTUlQjglQjglRTclOTQlQTglRTklODUlOEQlRTclQkQlQUUlRTUlOTIlOEMlRTUlQjglQjglRTclOTQlQTglRTYlOEYlOTIlRTQlQkIlQjYvJUU3JTlCJUFFJUU1JUJEJTk1JUU1JUIxJTk1JUU3JUE0JUJBLnBuZw?x-oss-process=image/format,png)



### 打开IDEA项目选择

![打开IDEA项目选择](https://img-blog.csdnimg.cn/20190919135317919.png)



### 代码自动提示快捷键

移除占用Alt+斜杠的快捷键

![移除占用Alt+斜杠的快捷键](https://img-blog.csdnimg.cn/20190919135333462.png)

设置Basic快捷键为Alt+斜杠

![设置Basic快捷键为Alt+斜杠](https://img-blog.csdnimg.cn/20190919135358583.png)

 

### 全局修改文件描述信息（推荐）

```java
/**
 * Description: 
 *
 * @author JourWon
 * @date ${DATE} ${TIME}
 */
```



![统一修改文件描述信息](https://img-blog.csdnimg.cn/20190919135417548.png)


### 单个修改文件描述信息（不推荐）

去掉

```java
#parse("File Header.java")
```



添加

```java
/**
 * Description: 
 *
 * @author JourWon
 * @date ${DATE} ${TIME}
 */
```



![单个修改文件描述信息](https://img-blog.csdnimg.cn/20190919135434438.png)

 

### 生成方法注释

1.打开Preferences

2.Editor -> Live Templates -> 点击右边加号为自己添加一个Templates Group -> 然后选中自己的Group再次点击加号添加Live Templates

![生成方法注释1](https://img-blog.csdnimg.cn/20190919135512519.png)

3.然后设置自己喜欢的快捷键 在Abbreviation里面 记得在Applicable in 里面勾选Java

![生成方法注释2](https://img-blog.csdnimg.cn/20190919135523250.png)

4.然后在Edit variables里面添加参数和返回值的自动取值

```java
*
 * Description: $Description$
 *
 * @author JourWon
 * @date $DATE$ $TIME$
$param$
 * @return $return$
 */
```



![生成方法注释3](https://img-blog.csdnimg.cn/20190919135539209.png)

groovyScript脚本

```groovy
groovyScript("def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList(); for(i = 0; i < params.size(); i++) {result+=' * @param ' + params[i] + ((i < params.size() - 1) ? '\\n' : '')}; return result", methodParameters())
```

5.然后再你的方法上面直接输入`/*+*+Tab`



### 生成类注释

```java
/**
 * Description: $Description$
 * 
 * @author JourWon
 * @date $DATE$ $TIME$
 */
```



![生成类注释](https://img-blog.csdnimg.cn/20190919135558834.png)

在类上输入`cd+回车`，或者输入`cd+Tab`

 

### IDEA报错Could not autowire. No beans of 'xxxxMapper' type found

解决办法是：降低Autowired检测的级别，将Severity的级别由之前的error改成warning

![降低Autowired检测的级别](https://img-blog.csdnimg.cn/20190919135614841.png)



### IDEA生成序列号serialVersionUID

设置完成后，按Alt+Enter键，这个时候可以看到"Add serialVersionUID field"提示信息

![IDEA生成序列号serialVersionUID](https://img-blog.csdnimg.cn/20190919135641281.png)



### 导出导入配置

**导出配置**

file -> export setting, 设置导出的settings.jar包的位置，然后点击OK

![导出配置](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0lERUElRTUlQjglQjglRTclOTQlQTglRTklODUlOEQlRTclQkQlQUUlRTUlOTIlOEMlRTUlQjglQjglRTclOTQlQTglRTYlOEYlOTIlRTQlQkIlQjYvJUU1JUFGJUJDJUU1JTg3JUJBJUU5JTg1JThEJUU3JUJEJUFFLnBuZw?x-oss-process=image/format,png)

 

**导入配置**

file -> import settings，选择你想要导入的 settings.jar即可

 



## 常用插件

1. Alibaba Java Coding Guidelines -阿里巴巴代码规范检查插件

2. FindBugs-IDEA   -检查潜在bug插件

3. Free Mybatis plugin -Mybatis 辅助插件

4. GsonFormat -将JSON字符串转换为内部类实体类插件

5. Lombok plugin -简化实体类编写插件

6. Maven Helper -Maven辅助插件

7. SonarLint -代码质量检查插件

8. Translation -翻译插件

9. CodeGlance -代码地图

10. .ignore -git忽略文件

11. CamelCase -驼峰式转换插件

12. String Manipulation -一款强大的字符串转换工具

13. Key Promoter X -一款可以进行快捷键提示的插件

14. AceJump -一款可以彻底摆脱鼠标的插件

15. IDEA查看日志的插件

    ANSI Hignlighter - 高亮插件

    Ideolog – ide查看日志插件