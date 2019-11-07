# Linux(CentOS7)安装Tomcat与设置Tomcat为开机启动项

[toc]



## 安装Tomcat

### 下载Tomcat压缩包

Tomcat有Tomcat7，Tomcat8和Tomcat9等版本，目前企业使用较多的是Tomcat8，所以这里以Tomcat8为列

进入Tomcat8下载网址：[Tomcat8下载网址https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)

点击左侧Download下的对应版本，**这里我下载的是apache-tomcat-8.5.47.tar.gz，即Linux环境的压缩包**



**Tomcat主要有三个安装版本**

- tar.gz：Linux环境下的压缩包，免安装

- Windows.zip：Windows压缩包，免安装，解压即用，同时注意根据自己电脑是64位系统还是32位系统下载对应的压缩包

- Windows Service Installer：Windows安装包，32位和64位版本的Windows系统都适用


![在这里插入图片描述](https://img-blog.csdnimg.cn/20191024102415918.png)



### 安装Tomcat

将下载好的`apache-tomcat-8.5.47.tar.gz`放到指定目录，我这里放到`/usr/local/tomcat`，如下图所示

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191024102440484.png)



进入`/usr/local/tomcat`目录，解压Tomcat压缩包

```sh
# 进入/usr/local/tomcat目录
cd /usr/local/tomcat
# 解压Tomcat压缩包
tar -zxvf apache-tomcat-8.5.47.tar.gz
```



### 启动Tomcat

进入Tomcat的bin目录，启动Tomcat

```sh
# 进入Tomcat的bin目录，启动Tomcat
cd apache-tomcat-8.5.47/bin/

# 启动Tomcat
./startup.sh
```

查看Tomcat是否启动成功

```sh
# 查看Tomcat是否启动成功执行
ps -ef | grep tomcat
# 如果输出如下，说明Tomcat安装成功
root      2381     1 11 22:18 pts/0    00:00:02 /usr/local/jdk1.8.0_152/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/apache-tomcat-8.5.47/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 -Dignore.endorsed.dirs= -classpath /usr/local/tomcat/apache-tomcat-8.5.47/bin/bootstrap.jar:/usr/local/tomcat/apache-tomcat-8.5.47/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/tomcat/apache-tomcat-8.5.47 -Dcatalina.home=/usr/local/tomcat/apache-tomcat-8.5.47 -Djava.io.tmpdir=/usr/local/tomcat/apache-tomcat-8.5.47/temp org.apache.catalina.startup.Bootstrap start
root      2513 29060  0 22:18 pts/0    00:00:00 grep --color=auto tomcat
```



使用浏览器访问Tomcat，地址Linux的ip:8080，我这里的ip端口是` http://47.106.106.158:8080/ `，如下图说明在Linux（CentOS7）环境安装启动Tomcat成功，为自己点一个赞

**注意：开放8080端口或者关闭防火墙**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20191024102511751.png)



**附加：开放8080端口或者关闭防火墙，如果是阿里云只能在阿里云控制台配置开放端口**

```sh
# 开启8080端口
firewall-cmd --zone=public --add-port=8080/tcp --permanent

# 查询端口号8080是否开启
firewall-cmd --query-port=8080/tcp

# 重启防火墙
firewall-cmd --reload

# 查看开放端口列表
firewall-cmd --list-port

# 命令含义
--zone #作用域
--add-port=8080/tcp #添加端口，格式为：端口/通讯协议
--permanent #永久生效，没有此参数重启后失效

# 关闭防火墙
systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动
```



## 设置Tomcat为开机启动项

按照上面的方式启动Tomcat，如果我们的虚拟机或者服务器关闭了，重启服务器后Tomcat是关闭的，但是我们希望虚拟机或者服务器重启后，Tomcat可以自己启动，所以我们需要设置Tomcat为开机启动项



创建setenv.sh文件，为Tomcat添加启动参数

catalina.sh在执行的时候会调用同级路径下的setenv.sh来设置额外的环境变量，因此在`/usr/local/tomcat/apache-tomcat-8.5.47/bin`路径下创建setenv.sh文件，内容如下：

```sh
# 设置Tomcat的PID文件
CATALINA_PID="$CATALINA_BASE/tomcat.pid"
# 添加JVM选项
JAVA_OPTS="-server -XX:PermSize=256M -XX:MaxPermSize=1024m -Xms512M -Xmx1024M -XX:MaxNewSize=256m"
```



在`/usr/local/tomcat/apache-tomcat-8.5.47/bin/catalina.sh`文件开头添加`JAVA_HOME`和`JRE_HOME`，其中`/usr/local/jdk1.8.0_152`为jdk的安装目录

```sh
export JAVA_HOME=/usr/local/jdk1.8.0_152
export JRE_HOME=/usr/local/jdk1.8.0_152/jre
```

如果在`catalina.sh`不配置`JAVA_HOME`和`JRE_HOME`就会报如下的错误

```sh
[root@JourWon ~]# systemctl status tomcat
● tomcat.service - Tomcat
   Loaded: loaded (/usr/lib/systemd/system/tomcat.service; enabled; vendor preset: disabled)
   Active: failed (Result: exit-code) since Mon 2019-10-21 19:54:54 CST; 6s ago
  Process: 8746 ExecStart=/usr/local/tomcat/apache-tomcat-8.5.47/bin/startup.sh (code=exited, status=1/FAILURE)

Oct 21 19:54:54 JourWon systemd[1]: Starting Tomcat...
Oct 21 19:54:54 JourWon startup.sh[8746]: Neither the JAVA_HOME nor the JRE_...d
Oct 21 19:54:54 JourWon startup.sh[8746]: At least one of these environment ...m
Oct 21 19:54:54 JourWon systemd[1]: tomcat.service: control process exited,...=1
Oct 21 19:54:54 JourWon systemd[1]: Failed to start Tomcat.
Oct 21 19:54:54 JourWon systemd[1]: Unit tomcat.service entered failed state.
Oct 21 19:54:54 JourWon systemd[1]: tomcat.service failed.
Hint: Some lines were ellipsized, use -l to show in full.
```



在/usr/lib/systemd/system路径下添加tomcat.service文件，内容如下：

```sh
[Unit]
Description=Tomcat
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
TimeoutSec=0
PIDFile=/usr/local/tomcat/apache-tomcat-8.5.47/tomcat.pid
ExecStart=/usr/local/tomcat/apache-tomcat-8.5.47/bin/startup.sh
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

此外service文件修改后需要调用`systemctl daemon-reload`命令重新加载。 



配置` TimeoutSec=0 `的目的是让开机启动不处理tomcat启动超时，保证tomcat耗时过长时不会被系统terminating，如果不配置可能出现下面的情况

```sh
Oct 21 20:26:37 JourWon startup.sh[1634]: Existing PID file found during start.
Oct 21 20:26:37 JourWon startup.sh[1634]: Removing/clearing stale PID file.
Oct 21 20:26:37 JourWon startup.sh[1634]: Tomcat started.
Oct 21 20:26:37 JourWon systemd[1]: PID file /usr/local/tomcat/apache-tomcat-8.5.47/tomcat.pid not readable (yet?) after start.
Oct 21 20:26:38 JourWon polkitd[464]: Unregistered Authentication Agent for unix-process:1628:19013 (system bus name :1.23, object path /org/freedesktop/PolicyKit1/AuthenticationAgent, loca
Oct 21 20:28:07 JourWon systemd[1]: tomcat.service start operation timed out. Terminating.
Oct 21 20:28:07 JourWon systemd[1]: Failed to start Tomcat.
```



把Tomcat加入开机自启动

```sh
systemctl enable tomcat.service
```



重启服务器

```sh
reboot
```



再次连接后，查看服务状态

```sh
[root@JourWon ~]# systemctl status tomcat
● tomcat.service - Tomcat
   Loaded: loaded (/usr/lib/systemd/system/tomcat.service; enabled; vendor preset: disabled)
   Active: activating (start) since Mon 2019-10-21 20:12:19 CST; 8s ago
  Process: 9244 ExecStart=/usr/local/tomcat/apache-tomcat-8.5.47/bin/startup.sh (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/tomcat.service
           └─9255 /usr/local/jdk1.8.0_152/jre/bin/java -Djava.util.logging.config.file=/usr/local/tomcat/apache-tomcat-8.5.47/conf/logging.properties -Djava.util.logging.manager=org.apac...

Oct 21 20:12:19 JourWon systemd[1]: Starting Tomcat...
Oct 21 20:12:19 JourWon startup.sh[9244]: Existing PID file found during start.
Oct 21 20:12:19 JourWon startup.sh[9244]: Removing/clearing stale PID file.
Oct 21 20:12:19 JourWon startup.sh[9244]: Tomcat started.
Oct 21 20:12:19 JourWon systemd[1]: PID file /usr/local/tomcat/apache-tomcat-8.5.47/tomcat.pid not readable (yet?) after start.

```



查看开机启动列表命令

```sh
systemctl list-unit-files | grep enabled
```



 查看Tomcat是否设置为开机启动项，如果显示为enabled，说明设置成功

参数说明

- static：表示该服务与其他服务相关联,不能单独设置该服务的启动状态

- disabled：表示禁止开机启动

- enabled：表示允许开机启动

```sh
[root@JourWon ~]# systemctl list-unit-files | grep tomcat
tomcat.service                                enabled
```

