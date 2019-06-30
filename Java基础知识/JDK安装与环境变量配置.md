# JDK安装与环境变量配置

## 下载JDK

 [到Oracle官网下载JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

![下载JDK1](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/下载JDK1.png)

![下载JDK2](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/下载JDK2.png)

## 安装JDK

安装JDK时，除了修改安装目录，其他的一路【下一步】，傻瓜式安装。

注：当提示安装JRE时，可以选择不要安装。因为JDK已经自带了JRE。

安装JDK

![修改JDK安装目录](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/修改JDK安装目录.png)

## 测试JDK是否安装成功

在配置好环境变量后，可以进入cmd中检查Java是否安装正确，检查的命令为 java -version

- JDK安装成功

  ![测试JDK是否安装成功](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/测试JDK是否安装成功.png)

## 环境变量详解

### JAVA_HOME

- 变量名：JAVA_HOME
变量值：C:\develop\Java\jdk1.8.0_191
用途：定义一个变量，供其他地方使用

### Path

- 变量名：Path
变量值：%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
用途：让系统在任何路径下都可以识别java、javac、javap等命令

### CLASSPATH

- 变量名：CLASSPATH
变量值：.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
用途：告诉jvm要使用或执行的class放在什么路径上，便于JVM加载class文件，.;表示当前路径，tools.jar和dt.jar为类库路径

### CLASSPATH详解

- tools.jar
       工具类库(编译和运行等)，它跟我们程序中用到的基础类库没有关系。我们注意到在Path中变量值bin目录下的各个exe工具的大小都很小，一般都在27KB左右，这是因为它们实际上仅仅相当于是一层代码的包装，这些工具的实现所要用到的类库都在tools.jar中，用压缩软件打开tools.jar,你会发现有很多文件是和bin目录下的exe工具相对性的。当然，如果tools.jar的功能只有这些的话，那么我们根本不用把它加入到CLASSPATH变量中，因为bin目录下的工具自己可以完成对这些类库的调用，因此tools.jar应该还有其他的功能。在里面还可以看到有Applet和RMI等相关的文件，因此tools.jar应该还是远程调用等必须的jar包。tools.jar的其他作用可以查看其他资料。
dt.jar
       运行环境类库，主要是Swing包，这一点通过用压缩软件打开dt.jar也可以看到。如果在开发时候没有用到Swing包，那么可以不用将dt.jar添加到CLASSPATH变量中。
       CLASSPATH中的类库是由Application ClassLoader或者我们自定义的类加载器来加载的，这里当然不能包括基础类库，如果包括基础类库的话，并用两个不同的自定义类加载器去加载该基础类，那它得到的该基础类就不是唯一的了，这样便不能保证Java类的安全性。
基本类库和扩展类库rt.jar
       基本类库是所有的 import java.* 开头的类，在 %JAVA_HOME%\jre\lib 目录下（如其中的 rt.jar、resource.jar ），类加载机制提到，该目录下的类会由 Bootstrap ClassLoader 自动加载，并通过亲委派模型保证了基础类库只会被Bootstrap ClassLoader加载，这也就保证了基础类的唯一性。
       扩展类库是所有的 import javax.* 开头的类，在 %JAVA_HOME%\jre\lib\ext 目录下，该目录下的类是由Extension ClassLoader 自动加载，不需要我们指定。
       rt.jar 默认就在根ClassLoader的加载路径里面,放在CLASSPATH也是多此一举。

## 配置环境变量

 单击"计算机-属性-高级系统设置"，单击"环境变量"。
(1)新建->变量名"JAVA_HOME"，变量值"C:\develop\Java\jdk1.8.0_191"（即JDK的安装路径） 
(2)编辑->变量名"Path"，点击"新建"，然后输入上"%JAVA_HOME%\bin"，点击"确定"，再次点击"新建"，然后输入上"%JAVA_HOME%\jre\bin"，点击确定。
(3)新建->变量名"CLASSPATH",变量值".;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar"

安装图

- 系统属性
![系统属性](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/系统属性.png)
- 新建系统变量JAVA_HOME
![新建环境变量](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/新建系统变量JAVA_HOME.png)
- 新建环境变量
![新建环境变量](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/新建环境变量.png)
- 新建系统变量CLASSPATH
![新建环境变量](https://raw.githubusercontent.com/JourWon/image/master/JDK安装与环境变量配置/新建系统变量CLASSPATH.png)
