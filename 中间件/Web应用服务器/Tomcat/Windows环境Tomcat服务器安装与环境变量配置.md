# Windows环境Tomcat服务器安装与环境变量配置

[toc]

Tomcat 服务器是一个免费的开放源代码的 Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP 程序的首选。本文主要讲述Windows环境Tomcat服务器安装与环境变量配置

## 下载安装JDK

要想安装Tomcat服务器，首先要安装配置好JDK，可以参考[JDK安装与环境变量配置](https://blog.csdn.net/ThinkWon/article/details/94353907)

Windows + R然后输入cmd进入命令行窗口，检查Java是否安装正确，检查的命令为`java -version` 

如果输出类似下面的内容说明安装成功

```java
java version "1.8.0_191"
Java(TM) SE Runtime Environment (build 1.8.0_191-b12)
Java HotSpot(TM) 64-Bit Server VM (build 25.191-b12, mixed mode)
```



## 下载Tomcat压缩包

Tomcat有Tomcat7，Tomcat8和Tomcat9，目前企业使用较多的是Tomcat8，所以这里以Tomcat8为列

进入Tomcat8下载网址：[Tomcat8下载网址https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)

点击左侧Download下的对应版本，**这里我下载的是64-bit Windows zip，即Windows 64位的zip包**



**Tomcat主要有三个安装版本**

- tar.gz：Linux环境下的压缩包，免安装

- Windows.zip：Windows压缩包，免安装，解压即用，**推荐安装**，同时注意根据自己电脑是64位系统还是32位系统下载对应的压缩包

- Windows Service Installer：Windows安装包，32位和64位版本的Windows系统都适用


![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/Tomcat8下载页面.png)



## 解压Tomcat压缩包

将下载好的zip包放到指定的位置，**注意：路径不能有中文和特殊字符**

我的zip包放在`C:\develop\Tomcat`

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/Tomcat压缩包.png)



然后解压到当前文件夹既可

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/解压Tomcat压缩包.png)



## Tomcat目录结构

解压Tomcat后的目录结构如下图

![](C:\Users\JourW\Desktop\Tomcat\Tomcat目录结构\Tomcat目录结构.png)

具体目录文件作用可参考[Tomcat目录结构](https://blog.csdn.net/ThinkWon/article/details/102619466)，说明写的非常详细哦



## 启动Tomcat

在Tomcat的bin目录里双击运行startup.bat文件，如果出现如下的界面，说明你的Tomcat服务器已经成功跑起来了，为自己点赞。

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/Tomcat启动界面.png)



然后在浏览器地址栏输入`127.0.0.1:8080`或者`localhost:8080`即可进入Tomcat主界面

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/Tomcat主界面.png)



但是有些小伙伴在启动Tomcat时也会遇到各种各样的问题，比如Tomcat的startup.bat启动后出现乱码，端口占用，启动后闪退等问题，具体可以参考[Tomcat常见问题汇总](https://blog.csdn.net/ThinkWon/article/details/102622824)



## 环境变量配置

Tomcat的环境变量配置跟JDK的环境变量配置几乎一样，只是修改变量名称和对应的路径，具体操作如下。

**注意**：如果不是经常需要全局使用Tomcat，个人不建议设置环境变量，因为可能与其他命令冲突，而且不像JDK使用得那么多，其实直接进入Tomcat目录启动也是很方便的。

在`此电脑`右键`属性`

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/右键此电脑.png)



单击"高级系统设置"，然后单击"环境变量"。

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/环境变量.png)



新建->变量名"CATALINA_HOME"，变量值"C:\develop\Tomcat\apache-tomcat-8.5.47"（即Tomcat的安装路径） 

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/新建环境变量.png)



在系统变量找到变量名"Path"，点击编辑，然后点击"新建"，然后输入上"%CATALINA_HOME%\bin"，点击"确定"，再点击"确定"。

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/新增Path变量.png)



测试环境变量是否配置成功，Windows + R然后输入cmd进入命令行窗口，检查的命令为startup.bat

如果输出如下则说明配置成功

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/配置好环境变量启动Tomcat.png)