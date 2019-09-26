# Maven简介

[TOC]

## 前言

> 在Java项目开发中，项目的编译、测试、打包等是比较繁琐的，属于重复劳动的工作，浪费人力和时间成本。以往开发项目时，程序员往往需要花较多的精力在引用jar包搭建项目环境上，跨部门甚至跨人员之间的项目结构都有可能不一样。Maven的仓库管理、依赖管理、继承和聚合等特性为项目的构建提供了一整套完善的解决方案。所以整理了有关Maven的知识，分享给大家。小编水平有限，有错误之处望大神指教。:+1::+1:



## 什么是Maven

> Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

从官网的介绍中我们可以看到Apache Maven是一个项目管理工具，它基于项目对象模型(POM)的概念，通过一小段描述信息来管理项目的构建、报告和文档。



## Maven的作用

Maven是跨平台的项目管理工具。主要服务于基于Java平台的项目构建，依赖管理和项目信息管理。

项目构建：

项目构建包括清理，···，编译，测试，报告，打包，部署···等步骤

理想的项目构建

高度自动化，跨平台，可重用的组件，标准化



传统方式管理jar包依赖的问题：

1. jar包冲突
2. jar包依赖
3. jar包体积过大
4. jar包在不同阶段无法个性化配置

使用maven方式管理jar包依赖的好处：

1. 解决jar包冲突
2. 解决jar包依赖问题
3. jar包不用再每个项目保存，只需要放在仓库即可
4. maven可以指定jar包的依赖范围

>  注：Maven安装与配置参考[Maven安装与配置](https://blog.csdn.net/ThinkWon/article/details/94346569) 

## Maven标准目录结构
若要使用Maven，那么项目的目录结构必须符合Maven的规范，其目录结构如下：
```java
project
	src
		main	项目主代码和资源
			java	项目的Java源代码
			resources	项目的资源文件
			webapp	web项目资源文件(可无)
		test	单元测试代码和资源
			java	测试的Java源代码
			resources	测试的资源文件(可无)
			
	target		打包输出目录(可无)
		classes	编译输出目录
		test-classes	测试编译输出目录
		
	pom.xml	
```



## Maven的几个核心概念

### POM
POM(Project Object Model)项目对象模型，一个项目所有的配置都放在POM文件中：定义项目的类型、名字、管理依赖关系，定制插件的行为等等。

Maven通过pom.xml文件来管理依赖和管理项目的构建生命周期，而项目构建的生命周期是依靠一个个的插件完成的。

![项目对象模型](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/项目对象模型.png)



### Maven仓库

Maven管理资源的位置。仓库里面包含依赖（jar包）和插件（plug-in）。Maven仓库分为本地仓库和远程仓库，而远程仓库又包括私服和中央仓库。 
>Maven仓库
>	本地仓库
>	远程仓库
>		私服
>		中央仓库
>		其他公共库

#### 本地仓库
用户自己电脑上的仓库，直接从本地获取。 

#### 远程仓库
##### 私服
私服是一种特殊的远程仓库，搭建在局域网内的仓库，私服代理广域网的仓库，提供给局域网内的用户使用，可用减少局域网内的用户与外界仓库的传输，每一个jar包只需要拉取一次就可以提供给局域网内所有的用户使用，并且也更加稳定。

>  注：Maven私服Nexus安装可参考[Maven私服Nexus安装与使用](https://blog.csdn.net/ThinkWon/article/details/94346681) 

##### 中央仓库

Maven官方提供的远程仓库，里面拥有最全的jar包资源，Maven首先从本地仓库中寻找项目所需的jar包，若本地仓库没有，再到Maven的中央仓库下载所需jar包。地址是：http://repo1.maven.org/maven2/。

![Maven仓库](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/Maven仓库.png)



### 坐标

在Maven中，坐标是jar包的唯一标识，Maven通过坐标在仓库中找到项目所需的jar包。

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.1.6.RELEASE</version>
</dependency>
```

- groupId：公司或组织域名倒序
- artifactId：模块名
- version：版本号
- packaging：项目的打包方式(pom/jar/war，默认jar)

groupId、artifactId、versioin简称GAV(Maven坐标)，是用来唯一标识jar包的。

最新最全的Maven依赖项版本查询网站：

> http://mvnrepository.com/



**Maven工程的坐标与仓库中路径的关系：**

Maven坐标和仓库对应的映射关系：

```java
[groupId][artifactId][version][artifactId]-[version].jar
```

对应本地仓库目录：

```java
org\springframework\spring-core\4.3.4.RELEASE\spring-core-4.3.4.RELEASE.jar
```



### 依赖传递

如果我们的项目引用了一个Jar包，而该Jar包又引用了其他Jar包，那么在默认情况下项目编译时，Maven会把直接引用和简洁引用的Jar包都下载到本地。

传递性依赖机制能够大大的简化依赖声明，而且大部分情况下我们只需要关心项目的直接依赖是什么，而**不用考虑这些直接依赖会引入什么传递性依赖**，但是当出现冲突了，则需要很清楚传递性依赖是从什么依赖路径引入的。

![依赖传递](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/依赖传递1.jpg)

WebMavenDemo项目依赖JavaMavenService1，JavaMavenService1项目依赖JavaMavenService2

pom.xml文件配置好依赖关系后，必须首先mvn install后，依赖的jar包才能使用。

- WebMavenDemo的pom.xml文件想能编译通过，JavaMavenService1必须mvn install
- JavaMavenService的pom.xml文件想能编译通过，JavaMavenService2必须mvn install

![依赖传递](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/依赖传递2.jpg)

为JavaMavenService2中增加了一个spring-core.jar包后，会惊喜的发现依赖的两个项目都自动的增加了这个jar包，这就是依赖的传递性。

> 注意：非compile范围的依赖是不能传递的。



### 依赖冲突

**依赖的原则**主要是为了解决模块之间jar包冲突问题。
我们分两种情况说明一下：

#### 路径最短者优先
例如：当我们工程Animal依赖Cat，Cat又依赖Action，每个单独工程中Action依赖log4j-1.2.17，Cat依赖log4j-1.2.14，那我们的Animal工程要依赖于哪个版本呢？其实Maven为我们提供了内置的原则，就是路径最短者优先，我们的Animal工程最终依赖的是log4j-1.2.14

![路径最短者优先](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/路径最短者优先.jpg)

#### 路径相同先声明优先
例如：当我们工程Animal同时依赖Cat，又依赖Action，每个单独工程中Action依赖log4j-1.2.17，Cat依赖log4j-1.2.14，这时候依赖的路径是相同的，那我们的Animal工程最终依赖的是哪个版本呢？

![路径相同先声明优先](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/路径相同先声明优先.jpg)

这个时候我们要看这两个jar文件在pom文件声明的先后顺序，优先声明者先依赖。



#### 统一管理依赖的版本：

```java
<properties>
	<!-- 在properties里面统一管理依赖的版本 -->  
	<spring-boot.version>2.1.3.RELEASE</spring-boot.version>
	<spring-cloud-starter-alibaba.version>0.9.0.RELEASE</spring-cloud-starter-alibaba.version>

	<maven.compiler.source>1.8</maven.compiler.source>
	<maven.compiler.target>1.8</maven.compiler.target>
</properties>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-web</artifactId>
		<version>${spring-boot.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
		<version>${spring-cloud-starter-alibaba.version}</version>
	</dependency>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-actuator</artifactId>
		<version>${spring-boot.version}</version>
	</dependency>
</dependencies>
```

为了统一管理版本号，可以使用properties标签，里面可以自定义版本的标签名。在使用的地方使用${自定义标签名}



### 依赖范围

依赖范围就是控制依赖在不同阶段的作用。**不同的依赖会使用不同的classpath**，在Maven中依赖的域有这几个：import、provided、runtime、compile、system、test。默认取值为compile。

![依赖范围](https://raw.githubusercontent.com/JourWon/image/master/Maven简介/依赖范围.png)



### 可选依赖和依赖排除

#### 可选依赖

```java
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <version>2.1.6.RELEASE</version>
    <!-- 不可以向下传递依赖 -->
    <optional>true</optional>
</dependency>
```

在导入一个依赖的时候，是否需要把这个依赖向下传递

false：可以向下传递（默认值）

true：不可以向下传递

 

#### 排除依赖

如果我们只想下载直接引用的Jar包，那么需要在pom.xml中做如下配置：(将需要排除的Jar包的坐标写在中)

```java
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
    <!-- 去掉springboot默认配置的logback依赖 -->  
    <exclusions>
        <exclusion>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-logging</artifactId>  
        </exclusion>  
    </exclusions>  
</dependency> 
```



### 聚合

1. 什么是聚合？

   将多个项目同时运行就称为聚合。聚合的作用，是为了简化构建项目的过程。一次性构建多个项目！

2. 如何实现聚合？

   只需在pom中作如下配置即可实现聚合

```java
<modules>
	<module>nacos-config-example</module>
	<module>nacos-discovery-example</module>
	<module>nacos-gateway-example</module>
</modues>
```



### 继承

1. 什么是继承？

   在聚合多个项目时，如果这些被聚合的项目中需要引入相同的Jar，那么可以将这些Jar写入父pom中，各个子项目继承该pom即可。

   继承的作用，为了简化pom.xml配置，简化groupId，artifactId， version ，还可以锁定子工程依赖版本。

2. 如何实现继承？

- 父pom配置：将需要继承的Jar包的坐标放入标签即可。

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.google.guava</groupId>
            <artifactId>guava</artifactId>
            <version>28.0-jre</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

- 子pom配置：

```xml
<parent>
    <groupId>父pom所在项目的groupId</groupId>
    <artifactId>父pom所在项目的artifactId</artifactId>
    <version>父pom所在项目的版本号</version>
</parent>
```




### 生命周期　　

Maven有三套相互独立的生命周期，请注意这里说的是“三套”，而且“相互独立”，初学者容易将Maven的生命周期看成一个整体，其实不然。这三套生命周期分别是：

**① Clean Lifecycle 在进行真正的构建之前进行一些清理工作。 **Clean生命周期一共包含了三个阶段：

- pre-clean 执行一些需要在clean之前完成的工作
- clean 移除所有上一次构建生成的文件
- post-clean 执行一些需要在clean之后立刻完成的工作



**② Default Lifecycle 构建的核心部分，编译，测试，打包，部署等等。**

- validate
- generate-sources
- process-sources
- generate-resources
- process-resources 复制并处理资源文件，至目标目录，准备打包
- compile 编译项目的源代码
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources 复制并处理资源文件，至目标测试目录
- test-compile 编译测试源代码
- process-test-classes
- test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署
- prepare-package
- package 接受编译好的代码，打包成可发布的格式，如 JAR
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install 将包安装至本地仓库，以让其它项目依赖。
- deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享

**总结**：不论你要执行生命周期的哪一个阶段，maven都是从这个生命周期的开始执行

**插件**：每个阶段都有插件（plugin）。插件的职责就是执行它对应的命令。



**③ Site Lifecycle 生成项目报告，站点，发布站点。**

- pre-site 执行一些需要在生成站点文档之前完成的工作
- site 生成项目的站点文档
- post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
- site-deploy 将生成的站点文档部署到特定的服务器上



## Maven常用操作

### 手动安装Maven依赖

在使用Maven的依赖Oracle的驱动包时，会出现依赖错误的情况，原因是版权原因，Oracle官方屏蔽了依赖，那么要在本地使用其数据驱动包，要怎么做呢？去Oracle官网下载依赖然后安装到本地仓库

```shell
mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc7 -Dversion=12.1.0.2 -Dpackaging=jar -Dfile=E:\ojdbc7.jar
```

-Dfile为jar包的位置，执行完maven命令，然后再引入依赖

```java
<dependency>
    <groupId>com.oracle</groupId>
    <artifactId>ojdbc7</artifactId>
    <version>12.1.0.2</version>
</dependency>
```



### 部署到远程仓库

#### 远程仓库的认证

大部分远程仓库无需认证就可以访问，但有时候出于安全方面的考虑，我们需要提供认证信息才能访问一些远程仓库。例如，组织内部有一个Maven仓库服务器，该服务器为每个项目都提供独立的Maven仓库，为了防止非法的仓库访问，管理员为每个仓库提供了一组用户名及密码。这时，为了能让Maven访问仓库内容，就需要配置认证信息。

配置认证信息和配置仓库信息不同，仓库信息可以直接配置在POM文件中，但是认证信息必须配置在settings.xml文件中，这是因为POM往往是被提交到代码仓库中供所有成员访问的，而settings.xml一般只放在本机。因此，在settings.xml中配置认证信息更为安全。

在${MAVEN_HOME}/conf/settings.xml文件中配置认证

```xml
<servers>
	<server>
		<id>releases</id>
		<username>admin</username>
		<password>admin123</password>
	</server>
	<server>
		<id>snapshots</id>
		<username>admin</username>
		<password>admin123</password>
	</server>
</servers>
```

#### 部署到远程仓库

私服的一大作用是部署第三方构件，包括组织内部生成的构件以及一些无法从外部仓库直接获取的构件。无论是日常开发中生成的构件，还是正式版本发布的构件，都需要部署到仓库中，供其他团队成员使用。


Maven除了能对项目进行编译、测试、打包之外，还能将项目生成的构建部署到仓库中。首先，需要编写项目的pom.xml文件。配置distributionManagement元素见下面。

> 注意：repository里的id需要和第一步里的server id名称保持一致

```xml
<project>
...
    <distributionManagement>
        <repository>
            <!--repository里的id需要和第一步里的server id名称保持一致-->
            <id>releases</id>
            <!--仓库名称-->
            <name>Releases</name>
            <!--私服仓库地址-->
            <url>http://10.172.0.201:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>Snapshot</name>
            <url>http://10.172.0.201:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
...
</project>
```

distributionManagement包含repository和snapshotRepository子元素，前者表示发布版本构建的仓库，后者表示快照版本的仓库。这两个元素下都需要配置id、name和url，id为该远程仓库的唯一标识，name是为了方便人阅读，url表示该仓库的地址。

配置正确后，在命令行运行mvn clean deploy，Maven就会将项目构建输出的构件部署到配置对应的远程仓库，如果项目当前的版本是快照版本，则部署到快照版本仓库地址，否则就部署到发布版本仓库地址。



### 多模块Maven项目

#### dependencyManagement

在项目开发过程中，有时一个项目下面包含了几个子模块，在多模块的情况，POM的配置应该要注意写什么呢？我们通过一个例子来说明下。
有这样一个工程，里面有A模块、B模块和C模块，A模块需要引入junit和log4j库，配置如下：

```java
<dependency>
    <groupId>junit</groupId>
    <artifactid>junit</artifactId>
    <version>4.11</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactid>log4j</artifactId>
    <version>1.2.16</version>
</dependency>
```

此时B模块也需要引入这两个库，配置如下：

```java
<dependency>
    <groupId>junit</groupId>
    <artifactid>junit</artifactId>
    <version>4.12</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactid>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
```

会发现A模块和B模块对junit和log4j库依赖的版本是不同的，出现这种情况是十分危险的，因为依赖不同版本的库可能会造成很多未知的风险。怎么解决不同模块之间对同一个库的依赖版本一样呢？Maven提供了优雅的解决办法，**使用继承机制以及dependencyManagement元素来解决这个问题。** 如果你在父模块中配置dependencies，那么所有的子模块都自动继承，不仅达到了依赖一致的目的，还省了大段的代码，但这样来做会存在问题的。比如B模块需要spring-aop模块，但是C模块不需要spring-aop模块，如果用dependencies在父类中统一配置，C模块中也会包含有spring-aop模块，不符合我们的要求。但是用dependencyManagement就没有这样的问题。**dependencyManagement只会影响现有依赖的配置，但不会引入依赖。** 这样我们在父模块中的配置可以更改为如下所示：

```java
<!-- dependencyManagement只会影响现有依赖的配置，但不会引入依赖。 -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

这段配置不会给任何子模块引入依赖，如果某个子模块需要junit和log4j，只需要这样配置即可：

```java
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
    </dependency>
</dependencies>
```

**在多模块Maven项目中，使用dependencyManagement能够有效地帮我们维护依赖一致性。**



#### pluginManagement

上面介绍了在多模块中对依赖库的管理，接下来介绍下对插件的管理。与dependencyManagement类似，我们可以使用pluginManagement元素管理插件。一个常见的用法就是我们希望项目所有模块的使用compiler插件的时候，都是用jdk1.8，以及指定Java源文件编码为UTF-8，这时可以在父模块的POM中如下配置pluginManagement：

```java
<build>
    <pluginManagement>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
        </plugins>
    </pluginManagement>
</build>
```

这段配置会被应用到所有子模块的compiler插件中，因为Maven内置了compiler插件与生命周期的绑定，因此子模块不需要任何maven-compiler-plugin的配置了。



## Maven常用命令

| 命令        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| mvn clean   | 清除原来的编译结果                                           |
| mvn compile | 编译                                                         |
| mvn test    | 运行测试代码，mvn test -Dtest=类名 //单独运行测试类          |
| mvn package | 打包项目，mvn package -Dmaven.test.skip=true //打包时不执行测试 |
| mvn install | 将项目打包并安装到本地仓库                                   |
| mvn deploy  | 发布到本地仓库或者服务器                                     |



## Maven常用插件

Maven本质上是一个插件框架，它的核心并不执行任何具体的构建任务，所有这些任务都交给插件来完成。下面说几个常用的插件：

**maven-compiler-plugin(编译插件)**

用来编译Java代码，在对Java代码进行编译的时候，可以指定使用哪个JDK版本来进行编译，配置如下所示：

```java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
    	<!-- 源代码使用jdk1.8支持的特性 -->
        <source>1.8</source> 
        <!-- 使用jdk1.8编译目标代码 -->
        <target>1.8</target> 
        	   <!-- 传递参数 -->
               <compilerArgs> 
                    <arg>-parameters</arg>
                    <arg>-Xlint:unchecked</arg>
                    <arg>-Xlint:deprecation </arg>
              </compilerArgs>
    </configuration>
</plugin>
```

**maven-resources-plugin(资源插件)**

Maven区别对待Java代码和资源文件，maven-resources-plugin则用来处理资源文件。默认的主资源文件目录是src/main/resources，很多时候会需要添加额外的资源文件目录，这个时候就可以通过配置maven-resources-plugin来实现，配置如下所示：

```java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-resources-plugin</artifactId>
    <version>3.1.0</version>
    <executions>
        <execution>
        	<!-- 与Maven编译生命周期绑定在一起 -->
            <phase>compile</phase> 
        </execution>
    </executions>
</plugin>
```

**maven-surefire-plugin(测试插件)**

Maven2/3中用于执行测试的插件不是maven-test-plugin，而是maven-surefire-plugin，其实在大部分情况下，只要你的测试类遵循通用的命令约定（以Test结尾，以TestCase结尾、或者Test开头），就几乎不用知晓该插件的存在。但是当你想要跳过测试、排除某些测试类、或者使用一些TestNG特性的时候，就要用到了maven-surefire-plugin的一些配置选项了，配置如下所示：

```java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.22.2</version>
    <configuration>
    	<!-- 跳过测试 -->
        <skipTests>true</skipTests> 
    </configuration>
</plugin>
```

**maven-clean-plugin(清除插件)**

主要作用就是清理构建目录下的全部内容，有些项目，构建时需要清理构建目录以外的文件，比如指定的库文件，这时候就需要配置<filesets>来实现了，配置如下所示：

```java
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-clean-plugin</artifactId>
    <version>3.1.0</version>
    <configuration>
        <!--<skip>true</skip>-->
        <!--<failOnError>false</failOnError>-->
        <!--当配置true时,只清理filesets里的文件,构建目录中得文件不被清理.默认是flase.-->
        <excludeDefaultDirectories>false</excludeDefaultDirectories>
        <filesets>
            <fileset>
                <!--要清理的目录位置-->
                <directory>${basedir}/logs</directory>
                <!--是否跟随符号链接 (symbolic links)-->
                <followSymlinks>false</followSymlinks>
             </fileset>
        </filesets>
    </configuration>
</plugin>
```

**maven-war-plugin(打包插件)**

主要作用就是用来打包的，在打包的时候经常需要排除一些文件，就需要对warSourceExcludes进行配置了，配置如下所示：

```java
<plugin>
    <artifactId>maven-war-plugin</artifactId>
    <version>3.2.3</version>
    <configuration>
        <warSourceExcludes>WEB-INF/lib/**</warSourceExcludes>
    </configuration>
</plugin>
```