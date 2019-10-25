# 史上最强Tomcat8性能优化

[toc]

## 授人以鱼不如授人以渔

本博客的目的不在于给出最佳配置，而是带领开发者，能够从实际情况出发，通过不断的调节tomcat和jvm参数，去发现吞吐量，平均响应时间和错误率等信息的变化，同时根据服务器的cpu和内存等信息，结合接口的业务逻辑，最好是测试使用率最高，并发最大，或者是最重要的接口(比如下单支付接口)，设置最优的tomcat和jvm配置参数。



## 目的

通过Tomcat性能优化可以提高网站的并发能力。

Tomcat服务器在JavaEE项目中使用率非常高，所以在生产环境对Tomcat的优化也变得非常重要了。

对于Tomcat的优化，主要是从2个方面入手，一是Tomcat自身的配置，另一个是Tomcat所运行的jvm虚拟机的调优。



## 服务器资源

服务器所能提供CPU、内存、硬盘的性能对处理能力有决定性影响。硬件我们不说了，这个方面是钱越多越好是吧。



## Tomcat配置优化

### Linux环境安装运行Tomcat8

具体的安装步骤可以参考[Linux（CentOS7）安装Tomcat与设置Tomcat为开机启动项]()

如果需要登录系统，必须配置tomcat用户，在安装完Tomcat后，进行如下操作

在`/conf/tomcat-users.xml`文件中的`<tomcat-users>`标签里面添加如下内容

```xml
<!-- 修改配置文件，配置tomcat的管理用户 -->
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="admin"/>
<role rolename="admin-gui"/>
<user username="tomcat" password="tomcat" roles="admin-gui,admin,manager-gui,manager"/>
```



如果是tomcat7，配置了tomcat用户就可以登录系统了，但是tomcat8中不行，还需要修改另一个配置文件，否则访问不了，提示403，打开`webapps/manager/META-INF/context.xml`文件

```xml
<!-- 将Valve标签的内容注释掉，保存退出即可 -->
<?xml version="1.0" encoding="UTF-8"?>

<Context antiResourceLocking="false" privileged="true" >
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
  <Manager sessionAttributeValueClassNameFilter="java\.lang\.(?:Boolean|Integer|Long|Number|String)|org\.apache\.catalina\.filters\.CsrfPreventionFilter\$LruCache(?:\$1)?|java\.util\.(?:Linked)?HashMap"/>
</Context>
```



打开浏览器进行访问`10.172.0.202:8080`

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/Tomcat界面.png)



点击“Server Status”，输入用户名、密码进行登录，tomcat/tomcat

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/Tomcat登录.png)



登录之后可以看到服务器状态等信息，主要包括服务器信息，JVM，ajp和http信息

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/Tomcat服务器状态.png)



### AJP连接

在服务状态页面中可以看到，默认状态下会启用AJP服务，并且占用8009端口。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/ajp界面.png)



**什么是AJP**

AJP（Apache JServer Protocol）
AJPv13协议是面向包的。WEB服务器和Servlet容器通过TCP连接来交互；为了节省SOCKET创建的昂贵代价，WEB服务器会尝试维护一个永久TCP连接到servlet容器，并且在多个请求和响应周期过程会重用连接。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/Web客户访问Tocmat服务器上JSP组件的两种方式.png)

我们一般是使用Nginx+Tomcat的架构，所以用不着AJP协议，把AJP连接器禁用。

修改conf下的server.xml文件，将AJP服务禁用掉即可。

```xml
<!-- 禁用AJP连接 -->
<!-- <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> -->
```

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/禁用ajp连接.png)



重启tomcat，查看效果。可以看到AJP服务已经不存在了。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/禁用ajp连接的服务器界面.png)



### 执行器（线程池）

在tomcat中每一个用户请求都是一个线程，所以可以使用线程池提高性能。

修改server.xml文件：

```xml
<!--将注释打开-->
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="500" minSpareThreads="50" prestartminSpareThreads="true" maxQueueSize="100"/>

<!--
参数说明：
maxThreads：最大并发数，默认设置 200，一般建议在 500 ~ 1000，根据硬件设施和业务来判断
minSpareThreads：Tomcat 初始化时创建的线程数，默认设置 25
prestartminSpareThreads： 在 Tomcat 初始化的时候就初始化 minSpareThreads 的参数值，如果不等于 true，minSpareThreads 的值就没啥效果了
maxQueueSize，最大的等待队列数，超过则拒绝请求
-->

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

保存退出，重启tomcat，查看效果。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/使用线程池.png)

在页面中显示最大线程数为-1，这个是正常的，仅仅是显示的问题，实际使用的是指定的值。如果配置了一个Executor，则该属性的任何值将被正确记录，但是它将被显示为-1 



### 3种运行模式

tomcat的运行模式有3种：

**bio**
	性能非常低下，没有经过任何优化处理和支持

**nio**
	nio(new I/O)，是Java SE 1.4及后续版本提供的一种新的I/O操作方式(即java.nio包及其子包)。Java nio是一个基于缓冲区、并能提供非阻塞I/O操作的Java API，因此nio也被看成是non-blocking I/O的缩写。它拥有比传统I/O操作(bio)更好的并发运行性能。**Tomcat8默认使用nio运行模式**。

**apr**
	安装起来最困难，但是从操作系统级别来解决异步的IO问题，大幅度的提高性能



对于每种协议，Tomcat都提供了对应的I/O方式的实现，而且Tomcat官方还提供了在每种协议下每种I/O实现方案的差异， HTTP协议下的处理方式如下表，详情可查看[Tomcat官网说明](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html )

|              | BIO              | NIO                 | NIO2                 | APR                 |
| :----------- | ---------------- | :------------------ | :------------------- | :------------------ |
| 类名         | `Http11Protocol` | `Http11NioProtocol` | `Http11Nio2Protocol` | `Http11AprProtocol` |
| 引用版本     | ≥3.0             | ≥6.0                | ≥8.0                 | ≥5.5                |
| 轮询支持     | 否               | 是                  | 是                   | 是                  |
| 轮询队列大小 | N/A              | `maxConnections`    | `maxConnections`     | `maxConnections`    |
| 读请求头     | 阻塞             | 非阻塞              | 非阻塞               | 阻塞                |
| 读请求体     | 阻塞             | 阻塞                | 阻塞                 | 阻塞                |
| 写响应       | 阻塞             | 阻塞                | 阻塞                 | 阻塞                |
| 等待新请求   | 阻塞             | 非阻塞              | 非阻塞               | 非阻塞              |
| SSL支持      | Java SSL         | Java SSL            | Java SSL             | Open SSL            |
| SSL握手      | 阻塞             | 非阻塞              | 非阻塞               | 阻塞                |
| 最大链接数   | `maxConnections` | `maxConnections`    | `maxConnections`     | `maxConnections`    |



推荐使用nio，在tomcat8中有最新的nio2，速度更快，建议使用nio2

设置nio2：

```xml
<Connector executor="tomcatThreadPool"  port="8080" protocol="org.apache.coyote.http11.Http11Nio2Protocol"
               connectionTimeout="20000"
               redirectPort="8443" />
```

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/运行模式nio2.png)

可以看到已经设置为nio2了。



## 部署测试用的web项目

为了方便测试性能，我们将部署一个java web项目，这个项目本身和本博客没有什么关系，仅仅用于测试。

> 注意：这里在测试时，我们使用一个新的tomcat，进行测试，后面再对其进行优化调整，再测试。



### 查看服务器信息

说明一下我的测试服务器配置，不同的服务器配置对Tomcat的性能会有所影响。

| 配置参数        | 参数值                               |
| --------------- | ------------------------------------ |
| Linux版本       | CentOS Linux release 7.2.1511 (Core) |
| 查看逻辑cpu个数 | 4                                    |
| 查看物理cpu个数 | 4                                    |
| 总内存          | 8G                                   |

CentOS7服务器环境信息查看命令

**查看Linux版本**

查看Linux版本：cat /etc/centos-release



**查看CPU个数**

查看逻辑cpu个数：cat /proc/cpuinfo | grep "processor" | wc -l

查看物理cpu个数：cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l

查看每个物理cpu的核数cores：cat /proc/cpuinfo | grep "cpu cores"

如果所有物理cpu的cores个数加起来小于逻辑cpu的个数，则该cpu使用了超线程技术。查看每个物理cpu中逻辑cpu的个数：cat /proc/cpuinfo | grep "siblings"



**查看内存使用情况**

查看内存占用情况：free -m

参数说明

Mem：内存的使用情况总览表。

total：机器总的物理内存 单位为：M

used：用掉的内存。

free：空闲的物理内存。

```sh
[root@localhost ~]# cat /etc/centos-release
CentOS Linux release 7.2.1511 (Core)

[root@localhost ~]# cat /proc/cpuinfo | grep "processor" | wc -l
4
[root@localhost ~]# cat /proc/cpuinfo | grep "physical id" | sort | uniq | wc -l
4

[root@localhost ~]# cat /proc/cpuinfo | grep "cpu cores"
cpu cores       : 1
cpu cores       : 1
cpu cores       : 1
cpu cores       : 1

[root@localhost ~]# free -m
              total        used        free      shared  buff/cache   available
Mem:           7825         850        6241           9         733        6714
Swap:          8063           0        8063
```



### 部署web应用

上传war包到linux服务器，然后进行部署

**我的web应用的名字叫tomcat-optimization，主要是提供了一个查询用户列表的接口，该接口会去阿里云数据库查询用户列表，没有任务业务逻辑的处理**。

```shell
# 删除tomcat的/webapps/ROOT目录的所有文件
cd /webapps/ROOT
rm -rf *

# 上传war包到tomcat的/webapps/ROOT，然后解压
jar -xvf tomcat-optimization.war
rm -rf tomcat-optimization.war

# 进入tomcat的/bin目录重启tomcat
cd /bin
./shutdown.sh
./startup.sh
```



访问接口地址： http://10.172.0.202:8080/user/listUser 

```json
[{
	"id": 1,
	"account": "lilei",
	"password": "123456",
	"userName": "李雷",
	"gender": 1,
	"age": 15,
	"birthday": "2001-01-01 01:01:38",
	"createTime": "2016-03-01 19:09:55"
}, {
	"id": 2,
	"account": "hanmeimei",
	"password": "123456",
	"userName": "韩梅梅",
	"gender": 0,
	"age": 14,
	"birthday": "2002-01-01 01:01:38",
	"createTime": "2016-03-01 19:09:55"
}, {
	"id": 3,
	"account": "lucy",
	"password": "123456",
	"userName": "露西",
	"gender": 0,
	"age": 13,
	"birthday": "2003-01-01 01:01:38",
	"createTime": "2016-03-01 19:09:55"
}]
```



### 使用Apache JMeter进行性能测试

 Apache JMeter是Apache组织开发的基于Java的压力测试工具。 我们借助于此工具进行测试，将测试出tomcat的吞吐量等信息。

#### 下载安装

下载地址：http://jmeter.apache.org/download_jmeter.cgi

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/JMeter下载页面.png)

**注意**：这里需要先安装好jdk8及其以上版本的环境，可以参考[JDK安装与环境变量配置]( https://blog.csdn.net/ThinkWon/article/details/94353907 )



直接将下载好的zip压缩包进行解压即可。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/JMeter目录结构.png)



进入bin目录，找到jmeter.bat文件，双机打开即可启动。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/JMeter的bin目录.png)



JMeter启动页面

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/JMeter图标.png)

JMeter主页面

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/JMeter主界面.png)



#### 修改语言

默认的主题是黑色风格的主题并且语言是英语，这样不太方便使用，所以需要修改下语言。

设置语言为简体中文。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/修改语言.png)

修改语言完成的界面

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/修改语言完成.png)



#### 创建接口的测试用例

测试接口之前需要调整Windows环境配置，不然会报如下错误

```java
JMeter java.net.BindException: Address already in use: connect
```

**出现原因**：
TCP/IP连接数不够或TIME_WAIT中存在很多链接，导致吞吐量低。

**解决方案**：
从问题的原因分析，有两种解决方案，一是增加预留给TCP/IP服务的临时端口的数量，二是加快被占用端口的释放速度。

**解决办法**：
1、打开注册表：regedit
2、HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\ Services\TCPIP\Parameters
3、新建 DWORD值，name：TCPTimedWaitDelay，value：30（十进制） –> 设置为30秒，默认是240秒
4、新建 DWORD值，name：MaxUserPort，value：65534（十进制） –> 设置最大连接数65534
5、重启系统 



第一步：设置测试计划的名称

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/设置测试计划的名称.png)



第二步：添加线程组，使用线程模拟用户的并发

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/创建线程组.png)



![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/线程组参数设置.png)

1000个线程，每个线程循环10次，也就是tomcat会接收到10000个请求。



第三步：添加http请求

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/添加http请求.png)

设置http请求

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/设置http请求参数.png)



第四步：添加请求监控

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/添加测试监控.png)



#### 启动与进行接口测试

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/启动测试.png)



#### 查看测试报告

在聚合报告中，重点看吞吐量。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/聚合报告.png)



## 调整Tomcat参数进行优化

通过上面测试可以看出，tomcat在不做任何调整时，吞吐量为697次/秒。这个吞吐量跟接口的业务逻辑关系很大，如果业务逻辑复杂，需要比较长时间计算的，可能吞吐量只有几十次/秒，我这里测试的时候没有添加任务业务逻辑，才会出现吞吐量为697次/秒的情况。**这里的吞吐量最好是经过多次测试取平均值，因为单次测试具有一定的随机性**

### 禁用AJP连接

修改conf下的server.xml文件，将AJP服务禁用掉即可。

```xml
<!-- <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> -->
```

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/禁用ajp连接.png)

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/禁用ajp聚合报告.png)

这里经过9次测试，测试结果如下704 730 736 728 730 727 714 708 735	平均是723

可以看到，禁用AJP服务后，吞吐量会有所提升。

当然了，测试不一定准确，需要多测试几次才能看出是否有提升。



### 设置线程池

通过设置线程池，调整线程池相关的参数进行测试tomcat的性能。有关线程池更多更详细的配置参考[Tomcat官网提供的配置详解](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html )

#### 最大线程数为150，初始为4

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="150" minSpareThreads="4" prestartminSpareThreads="true"/>

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/线程池500聚合报告.png)

经过9次测试，测试结果如下705 725 702 729 733 738 735 728	平均是724



#### 最大线程数为500，初始为50

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="500" minSpareThreads="50" prestartminSpareThreads="true"/>

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

测试结果：733 724 718 728 734 721 720 723	平均725

吞吐量为725次/秒，性能有所提升。



#### 最大线程数为1000，初始为200

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="1000" minSpareThreads="200" prestartminSpareThreads="true"/>

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

吞吐量为732，性能有所提升。

测试结果 737 729 730 738 735 726 725 740	平均732



#### 最大线程数为5000，初始为1000

是否是线程数最多，速度越快呢？ 我们来测试下。

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="5000" minSpareThreads="1000" prestartminSpareThreads="true"/>

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

测试结果 727 733 728 725 738 729 737 735 739	平均732

可以看到，虽然最大线程已经设置到5000，但是实际测试效果并不理想，并且平均的响应时间也边长了，所以单纯靠提升线程数量是不能一直得到性能提升的。



#### 设置最大等待队列数

默认情况下，请求发送到tomcat，如果tomcat正忙，那么该请求会一直等待。这样虽然可以保证每个请求都能请求到，但是请求时间就会边长。

有些时候，我们也不一定要求请求一定等待，可以设置最大等待队列大小，如果超过就不等待了。这样虽然有些请求是失败的，但是请求时间会虽短。典型的应用：12306。

```xml
<!--最大等待数为100-->
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="500" minSpareThreads="100" prestartminSpareThreads="true" maxQueueSize="100"/>

<!--在Connector中设置executor属性指向上面的执行器-->
<Connector executor="tomcatThreadPool" port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/添加等待队列.png)

测试结果：

- 平均响应时间：0.438秒，响应时间明显缩短
- 错误率：43.07%，错误率超过40%，也可以理解，最大线程为500，测试的并发为1000
- 吞吐量：1359次/秒，吞吐量明显提升

结论：响应时间、吞吐量这2个指标需要找到平衡才能达到更好的性能。



### 设置nio2的运行模式

将最大线程设置为500进行测试：

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="500" minSpareThreads="100" prestartminSpareThreads="true"/>

<!-- 设置nio2 -->
<Connector executor="tomcatThreadPool" port="8080" protocol="org.apache.coyote.http11.Http11Nio2Protocol"
               connectionTimeout="20000"
               redirectPort="8443" />
```

从测试结果可以看到，平均响应时间有缩短，吞吐量有提升，可以得出结论：nio2的性能要高于nio。



### 参数说明与最佳实践

具体参数参考[官网说明](https://tomcat.apache.org/tomcat-8.5-doc/config/executor.html)

#### 执行器参数说明(加粗是重点)

| **Attribute**                                                | **Description**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| threadPriority （优先级）                                    | (int) 执行程序中线程的线程优先级，默认值为 `5`（`Thread.NORM_PRIORITY`常量的值） |
| daemon（守护进程）                                           | (布尔) 线程是否应该是守护程序线程，默认值为 `true`           |
| namePrefix（名称前缀）                                       | (String) 执行程序创建的每个线程的名称前缀。单个线程的线程名称将为`namePrefix+threadNumber` |
| **maxThreads**（最大线程数）                                 | (int) 此池中活动线程的最大数量，默认为 `200`                 |
| **minSpareThreads**（最小活跃线程数）                        | (int) 始终保持活动状态的最小线程数（空闲和活动），默认值为 `25` |
| maxIdleTime（空闲线程等待时间）                              | (int) 空闲线程关闭之前的毫秒数，除非活动线程的数目小于或等于minSpareThreads。默认值为`60000`（1分钟） |
| **maxQueueSize**（最大的等待队里数，超过则请求拒绝）         | (int) 在我们拒绝执行之前可以排队等待执行的可运行任务的最大数量。默认值为`Integer.MAX_VALUE` |
| **prestartminSpareThreads**（是否在启动时就生成minSpareThreads个线程） | (boolean) 在启动执行程序时是否应启动minSpareThreads，默认值为 `false` |
| threadRenewalDelay（重建线程的时间间隔）                     | (long) 如果配置了[ThreadLocalLeakPreventionListener](https://tomcat.apache.org/tomcat-8.5-doc/config/listeners.html)，它将通知该执行程序已停止的上下文。上下文停止后，池中的线程将更新。为避免同时更新所有线程，此选项设置了任意两个线程之间的延迟。该值以毫秒为单位，默认值为`1000`ms。如果值为负，则不更新线程。 |



#### 执行器最佳实践

此最佳配置仅供参考

```xml
<Executor name="tomcatThreadPool" namePrefix="catalina-exec-"
        maxThreads="800" minSpareThreads="100" maxQueueSize="100"                                 prestartminSpareThreads="true"/>
```



#### 连接器参数说明

可以看到除了这几个基本配置外并无特殊功能，所以我们需要对 Connector 进行扩展。

其中Connector 支持参数属性可以参考[Tomcat官方网站](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html)，本文就只介绍些常用的。

##### 通用属性(加粗是重点)

| Attribute             | Description                                                  |
| :-------------------- | :----------------------------------------------------------- |
| allowTrace            | 如果需要服务器能够处理用户的HAED/TRACE请求，这个值应该设置为true，默认值是false |
| asyncTimeout          | 默认超不时候以毫秒为单位的异步恳求。若是没有指定，该属性被设置为10000（10秒） |
| **enableLookups**     | 设置为`true`是否要调用以 `request.getRemoteHost()`执行DNS查找以返回远程客户端的实际主机名。设置为`false`跳过DNS查找并改为以字符串形式返回IP地址（从而提高性能）。默认情况下，DNS查找被禁用。 |
| maxHeaderCount        | 容器允许的请求头字段的最大数目。请求中包含比指定的限制更多的头字段将被拒绝。值小于0表示没有限制。如果没有指定，默认设置为100。 |
| maxParameterCount     | 将被容器自动解析的最大数量的参数和值对（GET加上POST）。参数值对超出此限制将被忽略。值小于0表示没有限制。如果没有指定，默认为10000。请注意， FailedRequestFilter 过滤器可以用来拒绝达到了极限值的请求。 |
| **maxPostSize**       | 容器FORM URL参数解析将处理的POST的最大大小（以字节为单位）。可以通过将此属性设置为小于零的值来禁用该限制。如果未指定，则此属性设置为2097152（2兆字节）。请注意， [`FailedRequestFilter`](https://tomcat.apache.org/tomcat-8.5-doc/config/filter.html#Failed_Request_Filter) 可以使用拒绝超过此限制的请求。 |
| maxSavePostSize       | 将被容器在FORM或CLIENT-CERT认证中保存/缓冲的POST的最大尺寸（以字节为单位）。对于这两种类型的身份验证，在用户身份验证之 前，POST将被保存/缓冲。对于POST CLIENT-CERT认证，处理该请求的SSL握手和缓冲清空期间，POST将被缓存。对于Form认证，POST将被保存，同时用户将被重定向到登陆 表单。POST将被一直保留直到用户成功认证或者认证请求关联的会话超时。将此属性设置为-1可以禁用此限制。将此属性设置为0，POST数据在身份验证 过程中将不被保存。如果没有指定，该属性设置为4096（4千字节）。 |
| parseBodyMethods      | 以逗号分隔的HTTP方法列表，通过方法列表，等同于POST方法，request 正文将被解析成请求参数。这在RESTful应用程序要支持以POST式的语义解析PUT请求中是非常有用的。需要注意的是设置其他值（不是POST）会导致Tomcat的行为违反servlet规范的目的。在这里为了符合HTTP规范明确禁止HTTP方法TRACE。默认值是POST |
| **port**              | **连接器** 将在其上创建服务器套接字并等待传入连接的TCP端口号。您的操作系统将仅允许一个服务器应用程序侦听特定IP地址上的特定端口号。如果使用特殊值0（零），则Tomcat将随机选择一个空闲端口用于此连接器。这通常仅在嵌入式和测试应用程序中有用。 |
| **protocol**          | 设置协议以处理传入流量。默认值为 `HTTP/1.1`使用自动切换机制选择基于Java NIO的连接器或基于APR / native的连接器。如果`PATH`（Windows）或`LD_LIBRARY_PATH`（在大多数Unix系统上）环境变量包含Tomcat本机库，并且`AprLifecycleListener`用于初始化APR的库的`useAprConnector`属性设置为 `true`，则将使用APR /本机连接器。如果找不到本机库或未配置属性，则将使用基于Java NIO的连接器。请注意，APR /本机连接器的HTTPS设置与Java连接器的设置不同。<br/>要使用显式协议而不是依赖于上述自动切换机制，可以使用以下值：<br/>`org.apache.coyote.http11.Http11NioProtocol`-非阻塞Java NIO连接器<br/>`org.apache.coyote.http11.Http11Nio2Protocol`-非阻塞Java NIO2连接器-APR<br/>`org.apache.coyote.http11.Http11AprProtocol`/本地连接器。<br/>也可以使用自定义实现。<br/>看看我们的[连接器比较](https://tomcat.apache.org/tomcat-8.5-doc/config/http.html#Connector_Comparison)表。对于Java和Java连接器，http和https的配置相同。<br/>有关APR连接器和特定于APR的SSL设置的更多信息，请访问[APR文档](https://tomcat.apache.org/tomcat-8.5-doc/apr.html) |
| proxyName             | 如果这个连接正在使用的代理服务器配置，配置该属性指定的服务器的名称，可以调用request.getServerName（）返回。有关更多信息，请参见代理支持。 |
| proxyPort             | 如果这个连接正在使用的代理服务器配置，配置该属性指定服务器端口，可以调用request.getServerPort（）返回。有关更多信息，请参见代理支持。 |
| redirectPort          | 如果该连接器支持非SSL请求，并且接收到的请求为满足安全约束需要SSL传输， Catalina 将自动将请求重定向到指定的端口号。 |
| scheme                | 将该属性设置为你想调用request.getScheme（）返回的协议的名称。例如，对于SSL连接器，你会将此属性设置为“HTTPS ”。默认值是“ HTTP ”。 |
| secure                | 如果你想调用request.isSecure（）收到此连接器的请求返回true，请该该属性设置为true。您希望SSL连接器或非SSL连接器接收数据通过一个SSL加速器，像加密卡，SSL设备，甚至一个web服务器。默认值是假的。 |
| **URIEncoding**       | 解决我们的乱码问题，这将指定使用的字符编码，来解码URI字符。如果没有指定，ISO-8859-1将被使用。 |
| useBodyEncodingForURI | 这指定是否应该用于URI查询参数，而不是使用URIEncoding contentType中指定的编码。此设置兼容性Tomcat 4.1.x版（该版在contentType中指定编码，或者使用request.setCharacterEncoding的方法显式设置（参数为 URL传来的值）。默认值false。 |
| useIPVHosts           | 将该属性设置为true会导致Tomcat使用收到请求的IP地址，来确定将请求发送到哪个主机。默认值是假的。 |
| xpoweredBy            | 将此属性设置为true会导致Tomcat支持使用Servlet规范的通知，（在规范中推荐使用头字段）。默认值是假的。 |



##### 标准实现(加粗是重点)

除了上面列出的常见的连接器属性，标准的HTTP连接器（BIO，NIO和APR/native）都支持以下属性。

| Attribute                        | Description                                                  |
| :------------------------------- | :----------------------------------------------------------- |
| **acceptCount**                  | 当所有可能的请求处理线程都在使用时，传入连接请求的最大队列长度。当队列满时收到的任何请求将被拒绝。默认值是100。 |
| **acceptorThreadCount**          | 用于接受连接的线程的数量。在一个多CPU的机器上，增加该值，虽然你可能不会真正需要超过2个。此外，有很多非保持活动连接，您可能需要增加这个值。默认值是 1。 |
| acceptorThreadPriority           | 接收器线程的优先级。该线程用来接受新的连接。默认值是5（java.lang.Thread.NORM_PRIORITY常量）。更多这个优先级是什么意思的详细信息，请查看java.lang.Thread的类的JavaDoc 。 |
| address                          | 对于拥有多个IP地址的服务器，该属性指定哪个地址将被用于在指定端口上监听。默认情况下，该端口将被用于与服务器相关联的所有IP地址。 |
| bindOnInit                       | 控制连接器绑定时套接字的使用。缺省情况，当连接器被启动时套接字被绑定和当连接器被销毁时套接字解除绑定。如果设置为false，连接器启动时套接字被绑定，连接器停止时套接字解除绑定。 |
| compressableMimeType             | 该值是一个被用于HTTP压缩的逗号分隔的MIME类型列表。默认值是text / html类型，为text / xml，text / plain。 |
| **compression**                  | 通常会在ngnix里面配置压缩 ，开启压缩GZIP 为了节省服务器带宽，连接器可以使用HTTP/1.1 GZIP压缩。可接受的参数的值是“off ”（禁用压缩），“on ”（允许压缩，这会导致文本数据被压缩），“force ”（强制在所有的情况下压缩），或者一个整数值（这是相当于为“on”，但指定了输出之前被压缩的数据最小量）。如果不知道内容长度但被设置为“on”或更积极的压缩，输出的数据也将被压缩。如果没有指定，该属性被设置为“关”。 注意：这是使用压缩（节省您的带宽）和使用sendfile功能（节省你的CPU周期）之间的权衡。如果连接器支持sendfile功能，例如NIO连接，则使用sendfile将优先于压缩。症状是48 KB的静态文件将未压缩就发送。你可以如下文所述通过设置连接器的useSendfile属性来关闭sendfile，或在默认的conf/web.xml或者你的web应用的web.xml中配置DefaultServlet来改变sendfile的使用量阈值。 |
| compressionMinSize               | 如果压缩被设置为“on”，那么该属性可以用于指定在输出之前被压缩的数据的最小量。如果未指定，此属性默认为“2048”。 |
| connectionLinger                 | 连接器的套接字被关闭时的逗留秒数。如果没有指定，将使用默认的JVM。 |
| connectionTimeout                | 在将提交的请求URI行呈现之后，连接器将等待接受连接的毫秒数。使用值-1表示没有超时（即无限）。默认值是60000（60秒），但请注意，Tomcat的标准server.xml中，设置为20000（即20秒）。 |
| **connectionUploadTimeout**      | 上传数据过程中，指定的以毫秒为单位超时时间。只有在设置disableUploadTimeout为false有效。 |
| **disableUploadTimeout**         | 此标志允许servlet容器在数据上传时使用不同的连接超时，通常较长。如果没有指定，该属性被设置为true，禁用上传超时。 |
| **executor**                     | 指向Executor元素的引用。如果这个属性被设置，并且被命名的executor存在，连接器将使用这个executor，而其他所有线程相关属性将被忽略。请注意共享的executor如果没有指定到一个连接器，则该连接器将使用一个私有的，内部的executor来提供线程池。 |
| executorTerminationTimeoutMillis | The time that the private internal executor will wait for request processing threads to terminate before continuing with the process of stopping the connector. If not set, the default is 0 (zero) for the BIO connector and 5000 (5 seconds) for the NIO and APR/native connectors. |
| keepAliveTimeout                 | 此连接器在关闭连接之前将等待另一个HTTP请求的毫秒数。默认值是使用已设置的connectionTimeout属性的值。使用值-1表示没有超时（即无限）。 |
| **maxConnections**               | 在任何给定的时间服务器接受并处理的最大连接数。当这个数字已经达到了，服务器将不会接受任何连接，直到连接的数量降到低于此值。基于acceptCount的设置，操作系统可能仍然接受连接。默认值根据不同的连接器类型而不同。对于BIO，默认的是maxThreads的值，除非使用了Executor，在这种情况下默认值是executor的maxThreads值 。对于NIO的默认值是10000。APR /native的默认值是8192。 需要注意的是Windows系统的APR/native，所配置的值将减少到小于或等于maxConnections的1024的倍数的最大值。这样做是出于性能方面的考虑。如果设置的值-1，maxConnections功能被禁用，而且连接数将不做计算。 |
| maxExtensionSize                 | 限制HTTP区块请求中区块扩展的总长度。如果值为`-1`，则不施加任何限制。如果未指定，`8192`将使用默认值。 |
| maxHttpHeaderSize                | 请求和响应的HTTP头的（以字节为单位的）最大尺寸。如果没有指定，该属性被设置为8192（8 KB）。 |
| maxKeepAliveRequests             | HTTP请求最大长连接个数。将此属性设置为1，将禁用HTTP/1.0、以及HTTP/1.1的长连接。设置为-1，不禁用。如果没有指定，该属性被设置为100。 |
| maxSwallowSize                   | Tomcat会为中止的上载而吞下的请求正文字节的最大数量（不包括传输编码开销）。上载中止是指Tomcat知道将忽略请求主体，但客户端仍将其发送。如果Tomcat不吞咽该主体，则客户端不太可能看到响应。如果未指定，将使用默认值2097152（2兆字节）。小于零的值表示不应强制执行任何限制。 |
| **maxThreads**                   | 最多同时处理的连接数，Tomcat使用线程来处理接收的每个请求。这个值表示Tomcat可创建的最大的线程数。如果没有指定，该属性被设置为200。如果使用了execute将忽略此连接器的该属性，连接器将使用execute，而不是一个内部线程池来处理请求。 |
| maxTrailerSize                   | 限制一个分块的HTTP请求中的最后一个块的尾随标头的总长度。如果该值是-1，没有限制的被强加。如果没有指定，默认值是8192。 |
| **minSpareThreads**              | 始终保持运行最小线程数。如果没有指定，则默认为10。           |
| noCompressionUserAgents          | 该值是一个正则表达式（使用java.util.regex），匹配不应该使用压缩的HTTP客户端的用户代理标头。因为这些客户端，虽然他们宣称支持压缩功能，但实现不完整。默认值是一个空字符串（正则表达式匹配禁用）。 |
| processorCache                   | 协议处理器缓存Processor对象以提高性能。此设置规定了这些对象有多少能得到缓存。-1意味着无限制，默认为200。如果不使用Servlet 3.0的异步处理，一个好的默认是使用maxThreads设置。如果使用Servlet 3.0的异步处理，一个好的默认是使用maxThreads和最大预期的并发请求（同步和异步）的最大值中的较大值。 |
| restrictedUserAgents             | 该值是一个正则表达式（使用java.util.regex），匹配用户代理头的HTTP浏览器将不能使用HTTP/1.1或HTTP/1.0长连接，即使该浏览器宣称支持这些功能的。默认值是一个空字符串（正则表达式匹配禁用）。 |
| server                           | 覆盖服务器的HTTP响应头。如果设置了这个属性的值将覆盖Web应用程序设置的Tomcat的默认头和任何服务器头。如果没有设置，应用程序指定的任何值将被使用。如果应用程序没有指定一个值，那么Apache-Coyote/1.1将被使用。除非你是偏执狂，你将不再需要此功能。 |
| socketBuffer                     | 为套接字输出缓冲而提供的缓冲区的大小（以字节为单位）。-1可以被指定来禁止使用的缓冲区。默认情况下，一个9000个字节的缓冲区将被使用。 |
| **SSLEnabled**                   | 在连接器上使用此属性来启用SSL加密传输。如果要打开SSL握手/加密/解密，请设置true。默认值是false。当设置这个值为true时，为了传递正确的request.getScheme（）和 request.isSecure（）到servlets，你需要设置scheme和secure属性。更多信息请查看SSL支持。 |
| tcpNoDelay                       | 如果设置为true，TCP_NO_DELAY选项将被设置在服务器上的套接字上，在大多数情况下，这样可以提高性能。默认设置为true。 |
| threadPriority                   | 在JVM中请求处理线程的优先级。默认值是5（java.lang.Thread.NORM_PRIORITY常量值）。关于优先级的更多详细信息，请查看java.lang.Thread的类的JavaDoc |
| upgradeAsyncWriteBufferSize      | The default size of the buffer to allocate to for asynchronous writes that can not be completed in a single operation. Data that can't be written immediately will be stored in this buffer until it can be written. If more data needs to be stored than space is available in the buffer than the size of the buffer will be increased for the duration of the write. If not specified the default value of 8192 will be used. |



#### 连接器最佳实践

此最佳配置仅供参考

```xml
<Connector executor="tomcatThreadPool" port="8080" 						                            protocol="org.apache.coyote.http11.Http11Nio2Protocol" 
           connectionTimeout="20000" redirectPort="8443" 
           enableLookups="false" maxPostSize="10485760" URIEncoding="UTF-8" 	                    acceptCount="100" acceptorThreadCount="2" disableUploadTimeout="true"                    maxConnections="10000" SSLEnabled="false"/>
```



## 调整JVM参数进行优化

接下来，通过设置jvm参数进行优化，为了测试一致性，依然将最大线程数设置为500，启用nio2运行模式

### 设置并行垃圾回收器

在/bin/catalina.sh文件第一行添加如下参数，gc日志输出到`/logs/gc.log`

```shell
#年轻代、老年代均使用并行收集器，初始堆内存64M，最大堆内存512M
JAVA_OPTS="-XX:+UseParallelGC -XX:+UseParallelOldGC -Xms64m -Xmx512m -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -Xloggc:../logs/gc.log"
```

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/设置jvm参数聚合报告1.png)

测试结果与默认的JVM参数结果接近。



### 查看gc日志文件

将gc.log文件上传到gceasy.io查看gc中是否存在问题。上传文件后需要等待一段时间，需要耐心等待。

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/gceasy官网.png)



**问题一：系统所消耗的时间大于用户时间**

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/系统时间超过用户时间.png)

如果在报告中显示`System Time greater than User Time`，系统所消耗的时间大于用户时间，这反应出的服务器的性能存在瓶颈，调度CPU等资源所消耗的时间要长一些。



**问题二：线程暂停时间有点长**

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/keyper.png)

可以关键指标中可以看出，吞吐量表现不错，但是gc时，线程的暂停时间稍有点长。



**问题三：GC总次数过多**

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/gc总统计.png)

通过GC的统计可以看出：

- 年轻代的gc有100次，次数有点多，说明年轻代设置的大小不合适，需要调整
- FullGC有7次，说明堆内存的大小不合适，需要调整



**问题四：年轻代内存不足导致GC**

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/年轻代内存不足导致GC.png)

从GC原因的可以看出，年轻代大小设置不合理，导致了多次GC。



### 调整年轻代大小

调整jvm配置参数

```shell
JAVA_OPTS="-XX:+UseParallelGC -XX:+UseParallelOldGC -Xms128m -Xmx1024m -XX:NewSize=64m -XX:MaxNewSize=256m -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -Xloggc:../logs/gc.log"
```

将初始堆大小设置为128m，最大为1024m，初始年轻代大小64m，年轻代最大256m

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/设置jvm参数聚合报告2.png)

从测试结果来看，吞吐量以及响应时间均有提升。



查看gc日志

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/keyper2.png)



可以看到GC次数要明显减少，说明调整是有效的。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/gc总统计2.png)



GC次数有所减少

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/年轻代内存不足导致GC2.png)



### 设置G1垃圾回收器

```shell
#设置了最大停顿时间100毫秒，初始堆内存128m，最大堆内存1024m
JAVA_OPTS="-XX:+UseG1GC -XX:MaxGCPauseMillis=100 -Xms128m -Xmx1024m -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -Xloggc:../logs/gc.log"
```

测试结果

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/设置jvm参数聚合报告3.png)

可以看到，吞吐量有所提升，评价响应时间也有所缩短。

![img](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/keyper3.png)



G1集合阶段统计

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/史上最强Tomcat8性能优化/g1集合阶段统计.png)



### JVM配置最佳实践

此最佳配置仅供参考

```sh
JAVA_OPTS="-Dfile.encoding=UTF-8-server -Xms1024m -Xmx2048m -XX:NewSize=512m -XX:MaxNewSize=1024m -XX:PermSize=256m -XX:MaxPermSize=256m -XX:MaxTenuringThreshold=10-XX:NewRatio=2 -XX:+DisableExplicitGC"
```

**参数说明**：

file.encoding 默认文件编码

-Xmx1024m 设置JVM最大可用内存为1024MB

-Xms1024m 设置JVM最小内存为1024m。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。

-XX:NewSize 设置年轻代大小

-XX:MaxNewSize 设置最大的年轻代大小

-XX:PermSize 设置永久代大小

-XX:MaxPermSize 设置最大永久代大小

-XX:NewRatio=4 设置年轻代（包括Eden和两个Survivor区）与终身代的比值（除去永久代）。设置为4，则年轻代与终身代所占比值为1：4，年轻代占整个堆栈的1/5

-XX:MaxTenuringThreshold=0 设置垃圾最大年龄，默认为：15。如果设置为0的话，则年轻代对象不经过Survivor区，直接进入年老代。对于年老代比较多的应用，可以提高效率。如果将此值设置为一个较大值，则年轻代对象会在Survivor区进行多次复制，这样可以增加对象再年轻代的存活时间，增加在年轻代即被回收的概论。

-XX:+DisableExplicitGC 这个将会忽略手动调用GC的代码使得System.gc()的调用就会变成一个空调用，完全不会触发任何GC



## 总结

通过上述的测试，可以总结出，对tomcat性能优化就是需要不断的进行调整参数，然后测试结果，可能会调优也可能会调差，这时就需要借助于gc的可视化工具来看gc的情况。再帮我我们做出决策应该调整哪些参数。

**再次重申本博客的目的不在于给出最佳配置，而是带领开发者，能够从实际情况出发，通过不断的调节tomcat和jvm参数，去发现吞吐量，平均响应时间和错误率等信息的变化，同时根据服务器的cpu和内存等信息，结合接口的业务逻辑，最好是测试使用率最高，并发最大，或者是最重要的接口(比如下单支付接口)，设置最优的tomcat和jvm配置参数**。