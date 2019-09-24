# IDEA中Maven依赖下载失败解决方案

> 使用IDEA进行Maven项目开发时，时不时会遇到pom.xml报错的情况，其中很大概率是因为Maven依赖的jar包下载失败，找来找去也没有找到是什么问题，困扰了很多程序猿，这里给出IDEA中Maven依赖下载失败解决方案，给大家参考，实测有用。



## 首先检查网络有没有问题，确定网络没有问题，请看下一步



## 多次点击重新导入Maven依赖的按钮

![重新导入Maven依赖](https://raw.githubusercontent.com/JourWon/image/master/IDEA中Maven依赖下载失败解决方案/重新导入Maven依赖.png)

重新导入Maven依赖有两种方式，如上图所示。如果多次点击重新导入依赖按钮依然报错，请看下一步



## 设置自动导入Maven依赖

Settings -> Build,Execution,Deployment -> Build Tools -> Maven -> Importing，如下图

![自动导入Maven工程](https://raw.githubusercontent.com/JourWon/image/master/IDEA中Maven依赖下载失败解决方案/自动导入Maven工程.png)

这样设置后，如果Maven仓库存在依赖的话，IDEA会自动导入到项目中，如果没有用，看下一步



## 在IDEA中找到Maven的配置文件的地址，然后检查配置的远程仓库或者镜像有没有问题

![Maven配置.png](https://raw.githubusercontent.com/JourWon/image/master/IDEA中Maven依赖下载失败解决方案/Maven配置.png)

如上图所示，我的配置在`C:\develop\Maven\apache-maven-3.5.3\conf\settings.xml`

我配置的是阿里云仓库，没有什么问题，如果配置的是Maven私服Nexus的话，需要检查配置的路径和私服网络有没有问题

```xml
<!-- 配置阿里云仓库 -->
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>*</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```



## 如果上面几步都没有解决问题，可以使用以下脚本删除Mvaen中的lastUpdated文件

如果你的电脑是Windows系统，新建cleanLastUpdated.bat文件

> 注意：记得将脚本cleanLastUpdated.bat文件的仓库路径改为自己Maven仓库的路径

```bash
@echo off
  
rem 这里写你的仓库路径
set REPOSITORY_PATH=C:\develop\Maven\apache-maven-3.5.3\respository
rem 正在搜索...
for /f "delims=" %%i in ('dir /b /s "%REPOSITORY_PATH%\*lastUpdated*"') do (
    del /s /q %%i
)
rem 搜索完毕
pause
```

保存，然后双击执行脚本就可以删除lastUpdated文件，然后点击重新导入Maven依赖的按钮



## Maven仓库依赖存在依旧报错

![Maven仓库依赖存在依旧报错](https://raw.githubusercontent.com/JourWon/image/master/IDEA中Maven依赖下载失败解决方案/Maven仓库依赖存在依旧报错.png)

我的依赖问题到这一步才得到解决，好累，不过问题终于解决了！

有两种解决方式：

- 把pom.xml中对应的依赖先删除，然后刷新右侧，之后再把依赖粘贴到pom.xml中，再次刷新右侧就好了

- 从本地仓库将对应的包删除掉，然后让maven重新下载

  比如你要删除spring-boot-starter-web-2.1.8.RELEASE.jar，你要进入Maven仓库路径C:\develop\Maven\apache-maven-3.5.3\respository\org\springframework\boot\spring-boot-starter-web\2.1.8.RELEASE\spring-boot-starter-web-2.1.8.RELEASE.jar，然后进行删除