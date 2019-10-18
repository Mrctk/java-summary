# Tomcat目录结构

[toc]

Tomcat 服务器是一个免费的开放源代码的 Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP 程序的首选。十分有必要了解Tomcat目录结构。

## 目录结构

解压Tomcat后的目录结构如下图

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/Tomcat目录结构.png)


各目录及文件说明
| 目录及文件               | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| **bin**                  | 用于存放 Tomcat的启动、停止等批处理脚本和Shell脚本           |
| bin/startup. bat         | 用于在 Windows下启动 Tomcat                                  |
| bin/startup.sh           | 用于在 Linux下启动 Tomcat                                    |
| bin/shutdown. bat        | 用于在 Windows下停止 Tomcat                                  |
| bin/shutdown.sh          | 用于在 Linux下停止 Tomcat                                    |
| **conf**                 | 用于存放 Tomcat的相关配置文件                                |
| conf/Catalina            | 用于存储针对每个虚拟机的 Context 配置                        |
| conf/context.xml         | 用于定义所有Web应用均需要加载的 Context 配置，如果Web应用指定了自己的context.xml，那么该文件的配置将被覆盖 |
| conf/catalina.properties | Tomcat环境变量配置                                           |
| conf/catalina.policy     | 当 Tomcat在安全模式下运行时，此文件为默认的安全策略配置      |
| conf/logging.properties  | Tomcat日志配置文件，可通过该文件修改 Tomcat日志级别以及日志路径等 |
| conf/server.xml          | Tomcat服务器核心配置文件，用于配置 Tomcat的链接器、监听端口、处理请求的虚拟主机等。可以说，Tomcat主要根据该文件的配置信息创建服务器实例 |
| conf/tomcat-users.xml    | 用于定义 Tomcat默认用户及角色映射信息，Tomcat的 Manager模块即用该文件中定义的用户进行安全认证 |
| conf/web.xml             | Tomcat中所有应用默认的部署描述文件，主要定义了基础 Servlet和MIME映射。如果应用中不包含 Web. xml，那么 Tomcat将使用此文件初始化部署描述，反之，Tomcat会在启动时将默认部署描述与自定义配置进行合并 |
| **lib**                  | Tomcat服务器依赖库目录，包含 Tomcat服务器运行环境依赖lar包   |
| **logs**                 | Tomcat默认的日志存放路径                                     |
| **webapps**              | Tomcat默认的Web应用部署目录                                  |
| **work**                 | 存放Web应用JSP代码生成和编译后产生的class文件目录            |
| **temp**                 | 存放tomcat在运行过程中产生的临时文件                         |



## bin目录

用于存放 Tomcat的启动、停止等批处理脚本和Shell脚本

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/bin目录.png)



## conf目录

用于存放 Tomcat的相关配置文件

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/conf目录.png)



## lib目录

Tomcat服务器依赖库目录，包含 Tomcat服务器运行环境依赖lar包

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/lib目录.png)



## webapps目录

Tomcat默认的Web应用部署目录

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/webapps目录.png)



## temp目录

存放tomcat在运行过程中产生的临时文件

![](https://raw.githubusercontent.com/JourWon/image/master/Tomcat/Tomcat目录结构/temp目录.png)


