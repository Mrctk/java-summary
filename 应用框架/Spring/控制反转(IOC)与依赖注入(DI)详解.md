# 控制反转(IoC)与依赖注入(DI)详解

[toc]



学习了Spring框架的小伙伴们都知道Spring框架的核心是IoC容器和AOP模块。IoC容器是Spring框架的底层基础，负责管理对象的创建和对象依赖关系的维护。我们有必要好好学习IoC。

我们带着下面的5个问题来学习本博客

1. 什么是控制反转(IoC)
2. 控制反转(IoC)有什么作用
3. 控制反转(IoC)是怎么分类的
4. 什么是依赖注入(DI)
5. Spring的IoC容器是怎么进行依赖注入的



## 什么是控制反转(IoC)

控制反转即IoC (Inversion of Control)，它把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。



## 控制反转(IoC)有什么作用

- 管理对象的创建和依赖关系的维护。对象的创建并不是一件简单的事，在对象关系比较复杂时，如果依赖关系需要程序猿来维护的话，那是相当头疼的
- 解耦，由容器去维护具体的对象

- 托管了类的产生过程，比如我们需要在类的产生过程中做一些处理，最直接的例子就是代理，如果有容器程序可以把这部分处理交给容器，应用程序则无需去关心类是如何完成代理的



## 控制反转(IoC)是怎么分类的

控制反转IoC是一个很大的概念，可以用不同的方式来实现。其主要实现方式有两种：依赖注入和依赖查找

### 依赖注入

相对于IoC而言，依赖注入(DI)更加准确地描述了IoC的设计理念。所谓依赖注入（Dependency Injection），即组件之间的依赖关系由容器在应用系统运行期来决定，也就是由容器动态地将某种依赖关系的目标对象实例注入到应用系统中的各个关联的组件之中。组件不做定位查询，只提供普通的Java方法让容器去决定依赖关系。

Spring框架使用这种方式。依赖注入是时下最流行的IoC实现方式，依赖注入分为接口注入（Interface Injection），Setter方法注入（Setter Injection）和构造器注入（Constructor Injection）三种方式。其中接口注入由于在灵活性和易用性比较差，现在从Spring4开始已被废弃。 



依赖注入的基本原则是：应用组件不应该负责查找资源或者其他依赖的协作对象。配置对象的工作应该由IoC容器负责，“查找资源”的逻辑应该从应用组件的代码中抽取出来，交给IoC容器负责。容器全权负责组件的装配，它会把符合依赖关系的对象通过属性（JavaBean中的setter）或者是构造器传递给需要的对象。



依赖注入之所以更流行是因为它是一种更可取的方式：让容器全权负责依赖查询，受管组件只需要暴露JavaBean的setter方法或者带参数的构造器或者接口，使容器可以在初始化时组装对象的依赖关系。其与依赖查找方式相比，主要优势为：

- 查找定位操作与应用代码完全无关。
- 不依赖于容器的API，可以很容易地在任何容器以外使用应用对象。
- 不需要特殊的接口，绝大多数对象可以做到完全不必依赖容器。



#### 接口注入

```java
/**
 * Description:mapper接口
 *
 * @author JourWon
 * @date 2019/11/4 14:51
 */
public interface UserMapper {

    void createUserMapper(UserMapper userMapper);

    void test();

}
```

```java
/**
 * Description:service接口
 *
 * @author JourWon
 * @date 2019/11/4 16:39
 */
public interface UserService {

    void test();

}
```

```java
/**
 * Description:service实现类
 *
 * @author JourWon
 * @date 2019/11/4 14:55
 */
public class UserServiceImpl implements UserService, UserMapper {

    private UserMapper userMapper;

    @Override
    public void createUserMapper(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    @Override
    public void test() {
        userMapper.test();
    }

}
```



#### Setter方法注入

```java
public class UserServiceImpl implements UserService {

    private UserMapper userMapper;

    /**
     * setter方法注入
     * @param userMapper
     */
    public void setUserMapper(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    @Override
    public void test() {
        userMapper.test();
    }

}
```



#### 构造器注入

```java
public class UserServiceImpl implements UserService {

    private UserMapper userMapper;

    /**
     * 构造方法注入
     * @param userMapper
     */
    public UserServiceImpl(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    @Override
    public void test() {
        userMapper.test();
    }

}
```



### 依赖查找

依赖查找（Dependency Lookup）：容器提供回调接口和上下文环境给组件。EJB和Apache Avalon都使用这种方式。

依赖查找也有两种类型：依赖拖拽（DP）和上下文化依赖查找(CDL)。

#### 上下文依赖查找(Contextualized Dependency Lookup) 

在某些方面跟依赖拖拽类似，但是上下文依赖查找中，查找的过程是在容器管理的资源中进行的，而不是从集中注册表中，并且通常是作用在某些设置点上。

```java
public class UserServiceImpl {

    private DataSource dataSource;

    private UserMapper userMapper;

    public UserServiceImpl(){
        Context context = null;
        try{
            context = new InitialContext();
            dataSource = (DataSource) context.lookup("java:com/jourwon/dataSourceName");
            userMapper = (UserMapper) context.lookup("java:com/jourwon/UserMapperName");
        } catch (Exception e) {

        }
    }

}
```




#### 依赖拖拽 (Dependency Pull)

依赖拖拽：注入的对象如何与组件发生联系，这个过程就是通过依赖拖拽实现 。(较少有使用)

而通常对注入对象的配置可以通过一个 xml 文件完成。依赖拖拽就是通过这种方式对对象进行集中管理。

```java
public class UserServiceTest {
    
    public static void main(String[] args) {
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("classpath:spring-ioc.xml");
        UserServiceImpl userService = (UserServiceImpl) classPathXmlApplicationContext.getBean("UserServiceImpl");
        userService.test();
    }
    
}
```

通常我们对注入对象的配置可以通过一个xml文件完成。并使用这种方式对对象进行集中管理，使用依赖拖拽与依赖查找本质的区别是，依赖查找是在业务组件代码中进行的，而不是从集中注册表中，并且通常是作用在某些设置点上。



## Spring 的 IoC

Spring 的 IoC 设计支持以下功能：

- 依赖注入
- 依赖检查
- 自动装配
- 支持集合
- 指定初始化方法和销毁方法
- 支持回调某些方法（但是需要实现 Spring 接口，略有侵入）

其中，最重要的就是依赖注入，从 XML 的配置上说，即 ref 标签。对应 Spring RuntimeBeanReference 对象。

对于 IoC 来说，最重要的就是容器。容器管理着 Bean 的生命周期，控制着 Bean  的依赖注入。



### Spring 如何设计容器的

Spring 作者 Rod Johnson 设计了两个接口用以表示容器。

- BeanFactory
- ApplicationContext

BeanFactory 简单粗暴，可以理解为就是个 HashMap，Key 是 BeanName，Value 是 Bean 实例。通常只提供注册（put），获取（get）这两个功能。我们可以称之为 **“低级容器”**。

ApplicationContext 可以称之为 **“高级容器”**。因为他比 BeanFactory 多了更多的功能。他继承了多个接口。因此具备了更多的功能。例如资源的获取，支持多种消息（例如 JSP tag 的支持），对 BeanFactory 多了工具级别的支持等待。所以你看他的名字，已经不是 BeanFactory 之类的工厂了，而是 “应用上下文”， 代表着整个大容器的所有功能。该接口定义了一个 refresh 方法，此方法是所有阅读 Spring 源码的人的最熟悉的方法，用于刷新整个容器，即重新加载/刷新所有的 bean。

当然，除了这两个大接口，还有其他的辅助接口，这里就不介绍他们了。



### BeanFactory和ApplicationContext的关系

为了更直观的展示 “低级容器” 和 “高级容器” 的关系，这里通过常用的 ClassPathXmlApplicationContext 类，来展示整个容器的层级 UML 关系。

![img](https://raw.githubusercontent.com/JourWon/image/master/Spring/控制反转(IoC)与依赖注入(DI)详解/低级容器和高级容器的关系.png)

有点复杂？ 先不要慌，我来解释一下。

最上面的是 BeanFactory，下面的 3 个绿色的，都是功能扩展接口，这里就不展开讲。

看下面的隶属 ApplicationContext 粉红色的 “高级容器”，依赖着 “低级容器”，这里说的是依赖，不是继承哦。他依赖着 “低级容器” 的 getBean 功能。而高级容器有更多的功能：支持不同的信息源头，可以访问文件资源，支持应用事件（Observer 模式）。

通常用户看到的就是 “高级容器”。 但 BeanFactory 也非常够用啦！

左边灰色区域的是 “低级容器”， 只负载加载 Bean，获取 Bean。容器其他的高级功能是没有的。例如上图画的 refresh 刷新 Bean 工厂所有配置，生命周期事件回调等。



### 小结

说了这么多，不知道你有没有理解Spring  IoC？ 这里小结一下：IoC 在 Spring 里，只需要低级容器就可以实现，2 个步骤：

1. 加载配置文件，解析成 BeanDefinition 放在 Map 里。

2. 调用 getBean 的时候，从 BeanDefinition 所属的 Map 里，拿出  Class 对象进行实例化，同时，如果有依赖关系，将递归调用  getBean 方法 —— 完成依赖注入。


上面就是 Spring 低级容器（BeanFactory）的 IoC。

至于高级容器 ApplicationContext，他包含了低级容器的功能，当他执行 refresh 模板方法的时候，将刷新整个容器的 Bean。同时其作为高级容器，包含了太多的功能。一句话，他不仅仅是 IoC。他支持不同信息源头，支持 BeanFactory 工具类，支持层级容器，支持访问文件资源，支持事件发布通知，支持接口回调等等。