# Maven安装与配置

[toc]



## Maven安装

#### 1. 到Maven官网(http://maven.apache.org)下载软件


![Maven安装包](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU1JUFFJTg5JUU4JUEzJTg1JUU0JUI4JThFJUU5JTg1JThEJUU3JUJEJUFFL01hdmVuJUU1JUFFJTg5JUU4JUEzJTg1JUU1JThDJTg1LnBuZw)

> 注意：Maven软件依赖于Java，请先安装与配置好jdk
>
> 可参考[JDK安装与环境变量配置](https://blog.csdn.net/ThinkWon/article/details/94353907) 

#### 2. 直接解压apache-maven-3.6.0-bin.zip即可

   Maven目录结构

![Maven目录结构](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU1JUFFJTg5JUU4JUEzJTg1JUU0JUI4JThFJUU5JTg1JThEJUU3JUJEJUFFL01hdmVuJUU3JTlCJUFFJUU1JUJEJTk1JUU3JUJCJTkzJUU2JTlFJTg0LnBuZw)

#### 3. 把Maven的bin目录配置到环境变量中

   新建系统变量

   变量名：M2_HOME

   变量值：C:\develop\Maven\apache-maven-3.6.0

![新建系统变量](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU1JUFFJTg5JUU4JUEzJTg1JUU0JUI4JThFJUU5JTg1JThEJUU3JUJEJUFFLyVFNiU5NiVCMCVFNSVCQiVCQSVFNyVCMyVCQiVFNyVCQiU5RiVFNSU4RiU5OCVFOSU4NyU4Ri5wbmc)



​	在Path系统变量新增环境变量%M2_HOME%\bin，然后保存

![新建环境变量](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU1JUFFJTg5JUU4JUEzJTg1JUU0JUI4JThFJUU5JTg1JThEJUU3JUJEJUFFLyVFNiU5NiVCMCVFNSVBMiU5RSVFNyU4RSVBRiVFNSVBMiU4MyVFNSU4RiU5OCVFOSU4NyU4Ri5wbmc)

#### 4. 在命令提示符下输入mvn –version 

```shell
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T02:41:47+08:00)
Maven home: C:\develop\Maven\apache-maven-3.6.0\bin\..
Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: C:\develop\Java\jdk1.8.0_191\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

​	 如果你看到类似消息，说明 Apache Maven 在 Windows 上已安装成功。

> 注：Maven简介可参考[Maven简介](https://blog.csdn.net/ThinkWon/article/details/94346090) 



## Maven配置

### 配置本地仓库位置

Maven安装好之后默认配置了本地仓库，在%user%/.m2/respository目录，但是通常不会使用Maven的默认本地仓库，而是修改maven的本地仓库的地址，修改Maven目录的conf/settings.xml

```xml
<localRepository>C:\develop\Maven\apache-maven-3.6.0\respository</localRepository>
```



### 配置阿里云镜像

为了更好的下载速度，我们会选用国内镜像，这里配置的是阿里云镜像，修改Maven目录的conf/settings.xml

```xml
<!-- 配置阿里云镜像 -->
<mirror>
    <!--该镜像的唯一标识符。id用来区分不同的mirror元素。 -->
    <id>nexus-aliyun</id>
    <!--*指的是访问任何仓库都使用我们的私服-->
    <mirrorOf>*</mirrorOf>
    <!--镜像名称-->
    <name>Nexus aliyun</name>
    <!--该镜像的URL。构建系统会优先考虑使用该URL，而非使用默认的服务器URL-->
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror> 
```



### 设置Maven工程的默认jdk

创建Maven项目的时候，默认编译的jdk版本是1.7，但是我们需要使用的是jdk1.8版本，所以需要修改Maven目录的conf/settings.xml

```xml
<!-- 配置maven编译jdk版本 -->
<profile>    
    <id>jdk-1.8</id>    
    <activation>    
        <activeByDefault>true</activeByDefault>    
        <jdk>1.8</jdk>    
    </activation>    
    <properties>    
        <maven.compiler.source>1.8</maven.compiler.source>    
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion> 
    </properties>    
</profile>
```

