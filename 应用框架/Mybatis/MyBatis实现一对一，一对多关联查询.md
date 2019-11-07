# MyBatis实现一对一，一对多关联查询

[toc]



MyBatis如何实现一对一，一对多查询的？

可以通过关联查询实现。关联查询是几个表联合查询，只查询一次，通过在resultMap里面的association，collection节点配置一对一，一对多的类就可以完成



具体实现如下

## 准备数据库表结构与数据

班级classes与老师teacher是一对一的关系

班级classes与学生student是一对多的关系

```sql

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for classes
-- ----------------------------
DROP TABLE IF EXISTS `classes`;
CREATE TABLE `classes`  (
  `c_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '班级id',
  `c_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '班级名',
  `teacher_id` int(11) NULL DEFAULT NULL COMMENT '老师id',
  PRIMARY KEY (`c_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic COMMENT '班级表';

-- ----------------------------
-- Records of classes
-- ----------------------------
INSERT INTO `classes` VALUES (1, '高三2班', 1);
INSERT INTO `classes` VALUES (2, '高一4班', 2);

-- ----------------------------
-- Table structure for teacher
-- ----------------------------
DROP TABLE IF EXISTS `teacher`;
CREATE TABLE `teacher`  (
  `t_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '老师id',
  `t_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '老师姓名',
  PRIMARY KEY (`t_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic COMMENT '老师表';

-- ----------------------------
-- Records of teacher
-- ----------------------------
INSERT INTO `teacher` VALUES (1, '老师张三');
INSERT INTO `teacher` VALUES (2, '老师李四');

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for student
-- ----------------------------
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student`  (
  `s_id` int(11) NOT NULL AUTO_INCREMENT COMMENT '学生id',
  `s_name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL COMMENT '学生姓名',
  `class_id` int(11) NULL DEFAULT NULL COMMENT '班级id',
  PRIMARY KEY (`s_id`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic COMMENT '学生表';

-- ----------------------------
-- Records of student
-- ----------------------------
INSERT INTO `student` VALUES (1, '学生小明', 1);
INSERT INTO `student` VALUES (2, '学生小陈', 1);
INSERT INTO `student` VALUES (3, '学生小张', 2);

SET FOREIGN_KEY_CHECKS = 1;
```



数据库准备工作完成后，使用IDEA新建一个Spring Boot项目，工程目录如下

## 新建Spring Boot项目

![工程目录](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL015QmF0aXMlRTUlQUUlOUUlRTclOEUlQjAlRTQlQjglODAlRTUlQUYlQjklRTQlQjglODAlRUYlQkMlOEMlRTQlQjglODAlRTUlQUYlQjklRTUlQTQlOUElRTUlODUlQjMlRTglODElOTQlRTYlOUYlQTUlRTglQUYlQTIvJUU1JUI3JUE1JUU3JUE4JThCJUU3JTlCJUFFJUU1JUJEJTk1LnBuZw)



## 在pom.xml文件中添加如下依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



## 在application.yml文件中进行数据库和mybatis配置

```yaml
# spring配置
spring:
  # 数据库配置
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    url: jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&allowMultiQueries=true&useSSL=false&tinyInt1isBit=false&serverTimezone=GMT%2B8
    driverClassName: com.mysql.cj.jdbc.Driver
    username: root
    password: root

# mybatis配置
mybatis:
  # 给实体类配置别名
  type-aliases-package: com.jourwon.*.pojo
  # 加载mybatis的mapper配置文件
  mapper-locations: classpath:mybatis/mapper/**/*Mapper.xml
  configuration:
    # 开发环境控制台打印sql语句
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    # 开启驼峰规则自动映射字段属性值；如字段为user_name的可以映射到userName属性中
    map-underscore-to-camel-case: true
    # 设置sql执行超时时间,以秒为单位的全局sql超时时间设置,当超出了设置的超时时间时,会抛出SQLTimeoutException
    default-statement-timeout: 30
    # 解决查询返回结果含null没有对应字段值问题
    call-setters-on-nulls: true
```



## 新建老师类Teacher

```java
@Data
public class Teacher {

    private int id;

    private String name;

}
```



## 新建班级类Classes

```java
@Data
public class Classes {

    private int id;

    private String name;

    private Teacher teacher;

    private List<Student> studentList;


}
```



## 新建学生类Student

```java
@Data
public class Student {

    private int id;

    private String name;

}
```



## 新建ClassesMapper.xml映射文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.jourwon.mybatis.mapper.ClassesMapper">

    <!-- 一对一关联查询 -->
    <select id="listClasses" parameterType="int" resultMap="ClassesResultMap">
		select * from classes c,teacher t where c.teacher_id=t.t_id and c.c_id=#{id}
	</select>

    <resultMap type="com.jourwon.mybatis.pojo.Classes" id="ClassesResultMap">
        <!-- 实体类的字段名和数据表的字段名映射 -->
        <id property="id" column="c_id"/>
        <result property="name" column="c_name"/>
        <association property="teacher" javaType="com.jourwon.mybatis.pojo.Teacher">
            <id property="id" column="t_id"/>
            <result property="name" column="t_name"/>
        </association>
    </resultMap>

    <!-- 一对多关联查询 -->
    <select id="listClasses2" parameterType="int" resultMap="ClassesResultMap2">
		select * from classes c,teacher t,student s where c.teacher_id=t.t_id and c.c_id=s.class_id and c.c_id=#{id}
	</select>

    <resultMap type="com.jourwon.mybatis.pojo.Classes" id="ClassesResultMap2">
        <id property="id" column="c_id"/>
        <result property="name" column="c_name"/>
        <association property="teacher" javaType="com.jourwon.mybatis.pojo.Teacher">
            <id property="id" column="t_id"/>
            <result property="name" column="t_name"/>
        </association>
        <collection property="studentList" ofType="com.jourwon.mybatis.pojo.Student">
            <id property="id" column="s_id"/>
            <result property="name" column="s_name"/>
        </collection>
    </resultMap>


</mapper>
```



## Application类

```java
@MapperScan(basePackages = {"com.jourwon.mybatis.**.mapper"})
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```



## 测试类

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {

    @Autowired
    private ClassesMapper classesMapper;

    /**
     * Description: 测试班级与老师一对一关联关系
     *
     * @author JourWon
     * @date 2019/9/24 23:18
     * @param 
     * @return void
     */
    @Test
    public void test01() {

        List<Classes> classes = classesMapper.listClasses(1);
        for (Classes aClass : classes) {
            System.out.println(classes);
        }

    }

    /**
     * Description: 测试班级与学生一对多关联关系
     *
     * @author JourWon
     * @date 2019/9/24 23:19
     * @param 
     * @return void
     */
    @Test
    public void test02() {

        List<Classes> classes = classesMapper.listClasses2(1);
        for (Classes aClass : classes) {
            System.out.println(classes);
        }

    }

}
```



## 测试结果

test01()测试结果输出

```java
==>  Preparing: select * from classes c,teacher t where c.teacher_id=t.t_id and c.c_id=? 
==> Parameters: 1(Integer)
<==    Columns: c_id, c_name, teacher_id, t_id, t_name
<==        Row: 1, 高三2班, 1, 1, 老师张三
<==      Total: 1
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@140d1230]
[Classes(id=1, name=高三2班, teacher=Teacher(id=1, name=老师张三), studentList=null)]
```



test02()测试结果输出

```java
==>  Preparing: select * from classes c,teacher t,student s where c.teacher_id=t.t_id and c.c_id=s.class_id and c.c_id=? 
==> Parameters: 1(Integer)
<==    Columns: c_id, c_name, teacher_id, t_id, t_name, s_id, s_name, class_id
<==        Row: 1, 高三2班, 1, 1, 老师张三, 1, 学生小明, 1
<==        Row: 1, 高三2班, 1, 1, 老师张三, 2, 学生小陈, 1
<==      Total: 2
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@140d1230]
[Classes(id=1, name=高三2班, teacher=Teacher(id=1, name=老师张三), studentList=[Student(id=1, name=学生小明), Student(id=2, name=学生小陈)])]
```



所以，一对一关联查询需要在resultMap里面配置association，一对多关联查询需要在resultMap里面配置collection节点