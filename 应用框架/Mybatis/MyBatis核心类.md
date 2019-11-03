# MyBatis核心类

[toc]



>从MyBatis开发步骤可以看出，MyBatis主要运用到了一下几个类
## 1、SqlSessionFactoryBuilder

每一个MyBatis的应用程序的入口是SqlSessionFactoryBuilder。

它的作用是通过XML配置文件创建Configuration对象（当然也可以在程序中自行创建），然后通过build方法创建SqlSessionFactory对象。没有必要每次访问Mybatis就创建一次SqlSessionFactoryBuilder，通常的做法是创建一个全局的对象就可以了。示例程序如下：

```java
private static SqlSessionFactoryBuilder sqlSessionFactoryBuilder;
private static SqlSessionFactory sqlSessionFactory;

private static void init() throws IOException {
    String resource = "mybatis-config.xml";
    Reader reader = Resources.getResourceAsReader(resource);
    sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    sqlSessionFactory = sqlSessionFactoryBuilder.build(reader);
}

// org.apache.ibatis.session.Configuration 是mybatis初始化的核心。
```

mybatis-config.xml中的配置，最后会解析xml成Configuration这个类。

SqlSessionFactoryBuilder根据传入的数据流(XML)生成Configuration对象，然后根据Configuration对象创建默认的SqlSessionFactory实例。

## 2、SqlSessionFactory

SqlSessionFactory对象的主要功能是创建SqlSession对象，和SqlSessionFactoryBuilder对象一样，没有必要每次访问Mybatis就创建一次SqlSessionFactory，通常的做法是创建一个全局的对象就可以了。SqlSessionFactory对象一个必要的属性是Configuration对象，它是保存Mybatis全局配置的一个配置对象，通常由SqlSessionFactoryBuilder从XML配置文件创建。这里给出一个简单的示例：

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

	<settings>
		<!-- 开启驼峰规则自动映射字段属性值；如字段为user_name的可以映射到userName属性中 -->
		<setting name="mapUnderscoreToCamelCase" value="true" />
	</settings>

	<!-- 自定义类型别名 -->
	<typeAliases>
		<package name="com.jourwon.mybatis.pojo" />
	</typeAliases>

	<!-- 配置环境变量 -->
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url"
					value="jdbc:mysql://127.0.0.1:3306/test?useSSL=false" />
				<property name="username" value="root" />
				<property name="password" value="root" />
			</dataSource>
		</environment>
	</environments>

	<!-- 配置mappers -->
	<mappers>
		<mapper resource="mybatis/mappers/UserMapper.xml" />
	</mappers>

</configuration>

```

## 3、SqlSession

SqlSession对象的主要功能是完成一次数据库的访问和结果的映射，它类似于数据库的session概念，由于不是线程安全的，所以SqlSession对象的作用域需限制方法内。SqlSession的默认实现类是DefaultSqlSession，它有两个必须配置的属性：Configuration和Executor。Configuration前文已经描述这里不再多说。SqlSession对数据库的操作都是通过Executor来完成的。

SqlSession ：默认创建DefaultSqlSession 并且开启一级缓存，创建执行器 、赋值。

SqlSession有一个重要的方法getMapper，顾名思义，这个方式是用来获取Mapper对象的。什么是Mapper对象？根据Mybatis的官方手册，应用程序除了要初始并启动Mybatis之外，还需要定义一些接口，接口里定义访问数据库的方法，存放接口的包路径下需要放置同名的XML配置文件。

SqlSession的getMapper方法是联系应用程序和Mybatis纽带，应用程序访问getMapper时，Mybatis会根据传入的接口类型和对应的XML配置文件生成一个代理对象，这个代理对象就叫Mapper对象。应用程序获得Mapper对象后，就应该通过这个Mapper对象来访问Mybatis的SqlSession对象，这样就达到里插入到Mybatis流程的目的。



UserMapper接口

```java
public interface UserMapper {
	
	// 查询用户列表
	public List<User> selectAll();
    
}
```



UserMapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.jourwon.mybatis.mapper.UserMapper">

	<select id="selectAll" resultType="User">
		SELECT * FROM user
	</select>

</mapper>
```



测试类

```java
public class MybatisTest {

	private UserMapper userMapper;

	@Before
	public void init() throws Exception {
		String resource = "mybatis/mybatis-config.xml";
		// 获取配置文件流
		InputStream inputStream = Resources.getResourceAsStream(resource);
		// 创建会话工厂
		SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(inputStream);
		// 从工厂里获取会话对象
		SqlSession session = factory.openSession(true);
		
		userMapper = session.getMapper(UserMapper.class);
	}

	@Test
	public void tesSelectAll() {
		List<User> userList = userMapper.selectAll();
		for (User user : userList) {
			System.out.println(user);
		}
	}

}
```



## 4、Executor

Executor对象在创建Configuration对象的时候创建，并且缓存在Configuration对象里。Executor对象的主要功能是调用StatementHandler访问数据库，并将查询结果存入缓存中（如果配置了缓存的话）。

## 5、StatementHandler

StatementHandler是真正访问数据库的地方，并调用ResultSetHandler处理查询结果。

## 6、ResultSetHandler

处理查询结果。