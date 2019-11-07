# Maven依赖冲突

[toc]



**依赖的原则**主要是为了解决模块之间jar包冲突问题。
我们分两种情况说明一下：

## 路径最短者优先

例如：当我们工程Animal依赖Cat，Cat又依赖Action，每个单独工程中Action依赖log4j-1.2.17，Cat依赖log4j-1.2.14，那我们的Animal工程要依赖于哪个版本呢？其实Maven为我们提供了内置的原则，就是路径最短者优先，我们的Animal工程最终依赖的是log4j-1.2.14

![路径最短者优先](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU3JUFFJTgwJUU0JUJCJThCLyVFOCVCNyVBRiVFNSVCRSU4NCVFNiU5QyU4MCVFNyU5RiVBRCVFOCU4MCU4NSVFNCVCQyU5OCVFNSU4NSU4OC5qcGc)

## 路径相同先声明优先

例如：当我们工程Animal同时依赖Cat，又依赖Action，每个单独工程中Action依赖log4j-1.2.17，Cat依赖log4j-1.2.14，这时候依赖的路径是相同的，那我们的Animal工程最终依赖的是哪个版本呢？

![路径相同先声明优先](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL01hdmVuJUU3JUFFJTgwJUU0JUJCJThCLyVFOCVCNyVBRiVFNSVCRSU4NCVFNyU5QiVCOCVFNSU5MCU4QyVFNSU4NSU4OCVFNSVBMyVCMCVFNiU5OCU4RSVFNCVCQyU5OCVFNSU4NSU4OC5qcGc)

这个时候我们要看这两个jar文件在pom文件声明的先后顺序，优先声明者先依赖。



## 统一管理依赖的版本

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