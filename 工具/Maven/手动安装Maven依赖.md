# 手动安装Maven依赖

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

