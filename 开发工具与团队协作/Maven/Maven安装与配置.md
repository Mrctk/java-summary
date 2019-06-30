# Maven安装与配置

## Maven安装

#### 1. 到Maven官网(http://maven.apache.org)下载软件


![Maven安装包](https://raw.githubusercontent.com/JourWon/image/master/Maven安装与配置/Maven安装包.png)

> 注意：Maven软件依赖于Java，请先安装与配置好jdk

#### 2. 直接解压apache-maven-3.6.0-bin.zip即可

   Maven目录结构

![Maven目录结构](https://raw.githubusercontent.com/JourWon/image/master/Maven安装与配置/Maven目录结构.png)

#### 3. 把Maven的bin目录配置到环境变量中

   新建系统变量

   变量名：M2_HOME

   变量值：C:\develop\Maven\apache-maven-3.6.0

![新建系统变量](https://raw.githubusercontent.com/JourWon/image/master/Maven安装与配置/新建系统变量.png)



​	在Path系统变量新增环境变量%M2_HOME%\bin，然后保存

![新建环境变量](https://raw.githubusercontent.com/JourWon/image/master/Maven安装与配置/新增环境变量.png)

#### 4. 在命令提示符下输入mvn –version 

```shell
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T02:41:47+08:00)
Maven home: C:\develop\Maven\apache-maven-3.6.0\bin\..
Java version: 1.8.0_191, vendor: Oracle Corporation, runtime: C:\develop\Java\jdk1.8.0_191\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

​	 如果你看到类似消息，说明 Apache Maven 在 Windows 上已安装成功。



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
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
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

