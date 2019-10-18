# Tomcat常见问题汇总

[toc]

Tomcat 服务器是一个免费的开放源代码的 Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP 程序的首选。但是有些小伙伴在启动Tomcat时也会遇到各种各样的问题，比如Tomcat的startup.bat启动后出现乱码，端口占用，启动后闪退等问题，这里我们来一一进行解决

## 问题一：Tomcat的startup.bat启动后出现乱码

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat服务器下载、安装与环境变量配置/Tomcat启动界面.png)



找到Tomcat文件下的conf目录，修改logging.properties文件中java.util.logging.ConsoleHandler.encoding对应的值为GBK

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat常见问题汇总/修改编码为GBK.png)



重启Tomcat，你看到的启动页面是这样的话，说明乱码问题已经解决

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat常见问题汇总/修改编码后的启动界面.png)




## 问题二：一闪而退之端口占用

启动Tomcat出现一闪而退的现象，其实还是可以看到Tomcat终端输出的日志，这时你需要迅速截图捕获异常，太南了。

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat常见问题汇总/端口占用.png)



**最正确的方法是查看日志，找到Tomcat目录下的logs目录中catalina当天的日志**，我这里找到的是`C:\develop\Tomcat\apache-tomcat-8.5.47\logs\catalina.2019-10-17.log`，在日志中可以看到类似这样的输出，`Address already in use: bind`说明是端口占用了

```java
17-Oct-2019 11:14:30.521 严重 [main] org.apache.catalina.core.StandardService.initInternal Failed to initialize connector [Connector[HTTP/1.1-8080]]
	org.apache.catalina.LifecycleException: Protocol handler initialization failed
		at org.apache.catalina.connector.Connector.initInternal(Connector.java:995)
		at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:136)
		at org.apache.catalina.core.StandardService.initInternal(StandardService.java:552)
		at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:136)
		at org.apache.catalina.core.StandardServer.initInternal(StandardServer.java:848)
		at org.apache.catalina.util.LifecycleBase.init(LifecycleBase.java:136)
		at org.apache.catalina.startup.Catalina.load(Catalina.java:639)
		at org.apache.catalina.startup.Catalina.load(Catalina.java:662)
		at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
		at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
		at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
		at java.lang.reflect.Method.invoke(Method.java:498)
		at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:309)
		at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:492)
	Caused by: java.net.BindException: Address already in use: bind
		at sun.nio.ch.Net.bind0(Native Method)
		at sun.nio.ch.Net.bind(Net.java:433)
		at sun.nio.ch.Net.bind(Net.java:425)
		at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
		at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
		at org.apache.tomcat.util.net.NioEndpoint.bind(NioEndpoint.java:219)
		at org.apache.tomcat.util.net.AbstractEndpoint.init(AbstractEndpoint.java:1118)
		at org.apache.tomcat.util.net.AbstractJsseEndpoint.init(AbstractJsseEndpoint.java:224)
		at org.apache.coyote.AbstractProtocol.init(AbstractProtocol.java:581)
		at org.apache.coyote.http11.AbstractHttp11Protocol.init(AbstractHttp11Protocol.java:68)
		at org.apache.catalina.connector.Connector.initInternal(Connector.java:993)
		... 13 more
```



在Tomcat目录找到conf目录中的server.xml配置文件，在Connector标签中修改Tomcat启动端口

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat常见问题汇总/修改server配置文件启动端口.png)

```xml
<Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```



## 问题三：非端口问题的一闪而退

启动Tomcat直接一闪而退，在logs目录的日志里面找不到错误信息，这个时候很大可能是因为Tomcat与JDK版本不对应导致的，有可能你的Tomcat是Tomcat9版，而你的JDK是1.7版本的，Tomcat与JDK版本对应关系可以参考这篇文章[Tomcat与JDK版本对应关系，Tomcat各版本特性](https://blog.csdn.net/ThinkWon/article/details/102622738)

