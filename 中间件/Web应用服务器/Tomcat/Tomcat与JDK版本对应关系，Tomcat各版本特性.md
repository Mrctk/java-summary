# Tomcat与JDK版本对应关系，Tomcat各版本特性

[toc]



Apache Tomcat是一个开源软件实现了Java Servlet和Java Server Pages技术。不同版本的Servlet和JSP规范可使用不同版本的Apache Tomcat。Tomcat与JDK版本对应关系为：

| **Servlet规格** | **JSP规范** | **EL规格** | **WebSocket规范** | **JASPIC规格** | **Apache Tomcat版本** | **最新发行版本** | **支持的Java版本**                     |
| :-------------- | :---------- | :--------- | :---------------- | :------------- | :-------------------- | :--------------- | :------------------------------------- |
| 4.0             | 2.3         | 3.0        | 1.1               | 1.1            | 9.0.x                 | 9.0.27           | 8及更高版本                            |
| 3.1             | 2.3         | 3.0        | 1.1               | 1.1            | 8.5.x                 | 8.5.47           | 7及更高版本                            |
| 3.1             | 2.3         | 3.0        | 1.1               | 不适用         | 8.0.x（已取代）       | 8.0.53（已取代） | 7及更高版本                            |
| 3.0             | 2.2         | 2.2        | 1.1               | 不适用         | 7.0.x                 | 7.0.96           | 6及更高版本 （WebSocket为7及更高版本） |
| 2.5             | 2.1         | 2.1        | 不适用            | 不适用         | 6.0.x（已归档）       | 6.0.53（已归档） | 5及更高版本                            |
| 2.4             | 2.0         | 不适用     | 不适用            | 不适用         | 5.5.x（已存档）       | 5.5.36（存档）   | 1.4及更高版本                          |
| 2.3             | 1.2         | 不适用     | 不适用            | 不适用         | 4.1.x（已归档）       | 4.1.40（已归档） | 1.3及更高版本                          |
| 2.2             | 1.1         | 不适用     | 不适用            | 不适用         | 3.3.x（已存档）       | 3.3.2（已存档）  | 1.1及更高版本                          |

下面将详细介绍这些版本，以帮助您确定哪个版本适合您。有关每个发行版的更多详细信息，请参见相关的发行说明。

请注意，尽管我们提供旧版本的下载和文档，例如Apache Tomcat 7.x，但我们强烈建议用户尽可能使用最新的稳定版Apache Tomcat。我们认识到跨主版本升级可能不是一件容易的事，并且列表中仍为旧版本的用户提供了一些支持。但是，由于社区驱动的支持方法，您的版本越旧，感兴趣或能够支持您的人就越少。



## Alpha / Beta / Stable

Tomcat主要有三个版本，主要版本的初始发行通常会从Alpha到Beta，再到Stable

**Alpha**版本可能包含规范中要求的大量未经测试/缺少的功能和/或重大错误，并且预计不会在任何时间段内稳定运行。

**Beta**版本可能包含一些未经测试的功能和/或许多相对较小的错误。Beta版本预计不会稳定运行。

**Stable**版本可能包含少量相对较小的错误。稳定的版本旨在用于生产用途，并有望在更长的时间内稳定运行。



## Apache Tomcat 9.x

**Apache Tomcat 9.x**是当前的开发重点。它建立在Tomcat 8.0.x和8.5.x之上，并实现了**Servlet 4.0**，**JSP 2.3**，**EL 3.0**，**WebSocket 1.1** 和**JASPIC 1.1**规范（Java EE 8平台要求的版本）。除此之外，它还包括以下重要改进：

- 添加对HTTP / 2的支持（需要在Java 9上运行（自Apache Tomcat 9.0.0.M18起）或正在安装的[Tomcat Native](http://tomcat.apache.org/native-doc/)库）
- 添加了对将OpenSSL用于JSSE连接器（NIO和NIO2）的TLS支持的支持
- 增加了对TLS虚拟主机（SNI）的支持



## Apache Tomcat 8.x

**Apache Tomcat 8.0.x**建立在Tomcat 7.0.x之上，并实现了 **Servlet 3.1**，**JSP 2.3**，**EL 3.0** 和**WebSocket 1.1**规范。除此之外，它还包括以下重要改进：

- 一个单一的公共资源实现，以替换早期版本中提供的多个资源扩展功能。

**Apache Tomcat 8.5.x**支持与Apache Tomcat 8.0.x相同的Servlet，JSP，EL和WebSocket规范版本。除此之外，它还实现了**JASPIC 1.1**规范。

它是2016年3月从Tomcat 9.0.0.M4（alpha）里程碑发行版派生而来的。它提供Tomcat 9.x代码库的HTTP / 2支持和其他功能，同时与Tomcat 8.0运行时和规范要求兼容。（当时无法创建稳定的Tomcat 9.0版本，在几年后才确定了Tomcat 9的Java EE规范）。

Tomcat 8.5被认为是Tomcat 8.0的替代品。请参阅 [迁移指南](http://tomcat.apache.org/migration.html)，以获取有关迁移到Tomcat 8.5的指导。

Apache Tomcat 8.5.x包括以下重要改进：

- 添加对HTTP / 2的支持（需要[Tomcat Native](http://tomcat.apache.org/native-doc/)库）
- 添加了对将OpenSSL用于JSSE连接器（NIO和NIO2）的TLS支持的支持
- 增加了对TLS虚拟主机（SNI）的支持

Apache Tomcat 8.5.x中删除了以下技术：

- HTTP和AJP连接器的BIO实现
- 支持Comet API

Tomcat8.5再许多领域发生了重大变化，从而提高了性能，稳定性和总拥有成本。有关详细信息，请参考Apache Tomcat 8.5更改日志。

Tomcat 8.0的用户应该意识到Tomcat 8.0现在已经[停止开发](http://tomcat.apache.org/tomcat-80-eol.html)。Tomcat 8.0.x的用户应升级到Tomcat 8.5.x或更高版本。



## Apache Tomcat 7.x

**Apache Tomcat 7.x**建立在Tomcat 6.0.x的改进之上，并实现了**Servlet 3.0**， **JSP 2.2**，**EL 2.2**和 **WebSocket 1.1**规范。除此之外，它还包括以下改进：

- Web应用程序内存泄漏检测和预防
- 改善Manager和Host Manager应用程序的安全性
- 通用CSRF保护
- 支持直接在Web应用程序中包含外部内容
- 重构（连接器，生命周期）和大量内部代码清理



## Apache Tomcat 6.x

**Apache Tomcat 6.x**以Tomcat 5.5.x的改进为基础，并实现了**Servlet 2.5**和 **JSP 2.1**规范。除此之外，它还包括以下改进：

- 内存使用优化
- 先进的IO功能
- 重构集群

Tomcat 6的用户应该知道Tomcat 6现在已经[停止开发](http://tomcat.apache.org/tomcat-60-eol.html)。Tomcat 6.x的用户应升级到Tomcat 7.x或更高版本。
