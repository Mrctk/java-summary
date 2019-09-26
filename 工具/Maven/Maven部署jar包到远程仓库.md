### Maven部署jar包到远程仓库

部署jar包到远程仓库主要包括两个部分：远程仓库认证，部署jar包到远程仓库

#### 远程仓库的认证

大部分远程仓库无需认证就可以访问，但有时候出于安全方面的考虑，我们需要提供认证信息才能访问一些远程仓库。例如，组织内部有一个Maven仓库服务器，该服务器为每个项目都提供独立的Maven仓库，为了防止非法的仓库访问，管理员为每个仓库提供了一组用户名及密码。这时，为了能让Maven访问仓库内容，就需要配置认证信息。

配置认证信息和配置仓库信息不同，仓库信息可以直接配置在POM文件中，但是认证信息必须配置在settings.xml文件中，这是因为POM往往是被提交到代码仓库中供所有成员访问的，而settings.xml一般只放在本机。因此，在settings.xml中配置认证信息更为安全。

在${MAVEN_HOME}/conf/settings.xml文件中配置认证

```xml
<servers>
	<server>
		<id>releases</id>
		<username>admin</username>
		<password>admin123</password>
	</server>
	<server>
		<id>snapshots</id>
		<username>admin</username>
		<password>admin123</password>
	</server>
</servers>
```

#### 部署jar包到远程仓库

私服的一大作用是部署第三方构件，包括组织内部生成的构件以及一些无法从外部仓库直接获取的构件。无论是日常开发中生成的构件，还是正式版本发布的构件，都需要部署到仓库中，供其他团队成员使用。

Maven除了能对项目进行编译、测试、打包之外，还能将项目生成的构建部署到仓库中。首先，需要编写项目的pom.xml文件。配置distributionManagement元素见下面。

> 注意：repository里的id需要和第一步里的server id名称保持一致

```xml
<project>
...
    <distributionManagement>
        <repository>
            <!--repository里的id需要和第一步里的server id名称保持一致-->
            <id>releases</id>
            <!--仓库名称-->
            <name>Releases</name>
            <!--私服仓库地址-->
            <url>http://10.172.0.201:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>Snapshot</name>
            <url>http://10.172.0.201:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
...
</project>
```

distributionManagement包含repository和snapshotRepository子元素，前者表示发布版本构建的仓库，后者表示快照版本的仓库。这两个元素下都需要配置id、name和url，id为该远程仓库的唯一标识，name是为了方便人阅读，url表示该仓库的地址。

配置正确后，在命令行运行mvn clean deploy，Maven就会将项目构建输出的构件部署到配置对应的远程仓库，如果项目当前的版本是快照版本，则部署到快照版本仓库地址，否则就部署到发布版本仓库地址。

