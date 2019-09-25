# Java简介

![Java图标](https://raw.githubusercontent.com/JourWon/image/master/Java简介/Java图标.png)

[TOC]

## 什么是Java

 Java是一门面向对象编程语言，不仅吸收了C++语言的各种优点，还摒弃了C++里难以理解的多继承、指针等概念，因此Java语言具有功能强大和简单易用两个特征。Java语言作为静态面向对象编程语言的代表，极好地实现了面向对象理论，允许程序员以优雅的思维方式进行复杂的编程 。



## 诞生背景

![Java之父-詹姆斯·高斯林](https://raw.githubusercontent.com/JourWon/image/master/Java简介/詹姆斯·高斯林1.jpg)

 任职于Sun公司(Stanford University Network斯坦福网络公司)的詹姆斯·高斯林和麦克·舍林丹等人于1990年代初开发Java语言的雏形，最初被命名为Oak，Oak的目标是作为家用电器等小型系统的编程语言，用于电视机、电话、闹钟、烤面包机等家用电器的控制和通信。由于这些智能化家电的市场需求没有预期的高，Sun公司放弃了该项计划。随着1990年代互联网的发展，Sun公司发现Oak在互联网上应用的前景，于是改造了Oak，于1995年5月以Java的名称正式发布。伴随着互联网的迅猛发展，Java逐渐成为重要的网络编程语言。



## 三大版本

### Java SE（J2SE，Java 2 Platform Standard Edition，标准版）

- Java SE 以前称为 J2SE。它允许开发和部署在桌面、服务器、嵌入式环境和实时环境中使用的 Java 应用程序。Java SE 包含了支持 Java Web 服务开发的类，并为Java EE和Java ME提供基础。

### Java EE（J2EE，Java 2 Platform Enterprise Edition，企业版）

- Java EE 以前称为 J2EE。企业版本帮助开发和部署可移植、健壮、可伸缩且安全的服务器端Java 应用程序。Java EE 是在 Java SE 的基础上构建的，它提供 Web 服务、组件模型、管理和通信 API，可以用来实现企业级的面向服务体系结构（service-oriented architecture，SOA）和 Web2.0应用程序。2018年2月，**Eclipse 宣布正式将 JavaEE 更名为 JakartaEE**

### Java ME（J2ME，Java 2 Platform Micro Edition，微型版）

- Java ME 以前称为 J2ME。Java ME 为在移动设备和嵌入式设备（比如手机、PDA、电视机顶盒和打印机）上运行的应用程序提供一个健壮且灵活的环境。Java ME 包括灵活的用户界面、健壮的安全模型、许多内置的网络协议以及对可以动态下载的连网和离线应用程序的丰富支持。基于 Java ME 规范的应用程序只需编写一次，就可以用于许多设备，而且可以利用每个设备的本机功能。



## 语言特点与应用场景

 Java语言具有简单性、面向对象、分布式、健壮性、安全性、跨平台性、可移植性、多线程与动态性等特点。Java语言可以编写桌面应用程序、Web应用程序、分布式系统和嵌入式系统等 。Java 快速、安全、可靠。从笔记本电脑到数据中心，从游戏控制台到超级计算机，从手机到互联网，Java 无处不在！



## Java技术体系

下图为Oracle官网提供的Java技术体系图

![Java技术体系](https://raw.githubusercontent.com/JourWon/image/master/Java简介/Java技术体系.png)





## JVM、JRE和JDK的关系

### JVM

Java Virtual Machine是Java虚拟机，Java程序需要运行在虚拟机上，不同的平台有自己的虚拟机，因此Java语言可以实现跨平台。

### JRE
Java Runtime Environment包括Java虚拟机和Java程序所需的核心类库等。核心类库主要是java.lang包：包含了运行Java程序必不可少的系统类，如基本数据类型、基本数学函数、字符串处理、线程、异常处理类等，系统缺省加载这个包

如果想要运行一个开发好的Java程序，计算机中只需要安装JRE即可。

### JDK
Java Development Kit是提供给Java开发人员使用的，其中包含了Java的开发工具，也包括了JRE。所以安装了JDK，就无需再单独安装JRE了。其中的开发工具：编译工具(javac.exe)，打包工具(jar.exe)等

### JVM&JRE&JDK关系图

![JVM&JRE&JDK关系图](https://raw.githubusercontent.com/JourWon/image/master/Java简介/JVM&JRE&JDK关系图.png)