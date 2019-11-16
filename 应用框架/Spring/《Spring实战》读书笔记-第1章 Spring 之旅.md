# 《Spring实战》读书笔记-第1章 Spring之旅

[toc]



本章内容：

- Spring的bean容器
- 介绍Spring的核心模块
- 更为强大的Spring生态系统
- Spring的新功能



## 1.1 简化Java开发

Spring是一个开源框架，最早由**Rod Johnson**创建，并在《Expert Oneon-One：J2EE Design and Development》这本著作中进行了介绍。

纵览全书，读者会发现Spring可以做非常多的事情。但归根结底，支撑Spring的仅仅是少许的基本理念，所有的理念都可以追溯到Spring最根本的使命上：**解决企业级应用开发的复杂性，即简化Java开发**。



Spring是如何简化Java开发的？为了降低Java开发的复杂性，**Spring采取了以下4种关键策略**：

* 基于POJO的轻量级和最小侵入性编程；
* 通过依赖注入和面向接口实现松耦合；
* 基于切面和惯例进行声明式编程；
* 通过切面和模板减少样板式代码。

几乎Spring所做的任何事情都可以追溯到上述的一条或多条策略。在本章的其他部分，我将通过具体的案例进一步阐述这些理念，以此来证明Spring是如何完美兑现它的承诺的，也就是简化Java开发。让我们先从基于POJO的最小侵入性编程开始。



### 1.1.1 激发POJO的潜能

如果你从事Java编程有一段时间了，那么你或许会发现（可能你也实际使用过）很多框架通过强迫应用继承它们的类或实现它们的接口从而导致应用与框架绑死。一个典型的例子是EJB 2时代的无状态会话bean。早期的EJB是一个很容易想到的例子，不过这种侵入式的编程方式在早期版本的Struts、WebWork、Tapestry以及无数其他的Java规范和框架中都能看到。

Spring竭力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范的类，相反，在基于Spring构建的应用中，它的类通常没有任何痕迹表明你使用了Spring。最坏的场景是，一个类或许会使用Spring注解，但它依旧是POJO。

Spring的非侵入编程模型意味着这个类在Spring应用和非Spring应用中都可以发挥同样的作用。



### 1.1.2 依赖注入

控制反转和依赖注入的关系和详解可以查看[这篇文章](https://blog.csdn.net/ThinkWon/article/details/102912332)

Spring可以做很多事情，它为企业级开发提供给了丰富的功能，但是这些功能的底层都依赖于它的两个核心特性，也就是**依赖注入（dependency injection，DI）**和**面向切面编程（aspect-oriented programming，AOP）**。

依赖注入这个词让人望而生畏，现在已经演变成一项复杂的编程技巧或设计模式理念。但事实证明，依赖注入并不像它听上去那么复杂。在项目中应用DI，你会发现你的代码会变得异常简单并且更容易理解和测试。

DI功能是如何实现的

任何一个有实际意义的应用（肯定比Hello World示例更复杂）都会由两个或者更多的类组成，这些类相互之间进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象（即它所依赖的对象）的引用，这将会导致高度耦合和难以测试的代码。

**耦合具有两面性（two-headed beast）**。一方面，紧密耦合的代码难以测试、难以复用、难以理解，并且典型地表现出“打地鼠”式的bug特性（修复一个bug，将会出现一个或者更多新的bug）。另一方面，一定程度的耦合又是必须的——完全没有耦合的代码什么也做不了。为了完成有实际意义的功能，不同的类必须以适当的方式进行交互。总而言之，耦合是必须的，但应当被小心谨慎地管理。

通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理它们的依赖关系，如图1.1所示，依赖关系将被自动注入到需要它们的对象当中去。

![](https://img-blog.csdnimg.cn/2019111612423744.png)

依赖注入会将所依赖的关系自动交给目标对象，而不是让对象自己去获取依赖。

**创建应用组件之间协作的行为通常称为装配（wiring）**。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。如果XML配置不符合你的喜好的话，Spring还支持使用Java来描述配置。

观察它如何工作

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。



### 1.1.3 应用切面

**DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件**。

**面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术**。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为**横切关注点**，因为它们会跨越系统的多个组件。

如果将这些关注点分散到多个组件中去，你的代码将会带来双重的复杂性。

* 实现系统关注点功能的代码将会重复出现在多个组件中。这意味着如果你要改变这些关注点的逻辑，必须修改各个模块中的相关实现。即使你把这些关注点抽象为一个独立的模块，其他模块只是调用它的方法，但方法的调用还是会重复出现在各个模块中。
* 组件会因为那些与自身核心业务无关的代码而变得混乱。一个向地址簿增加地址条目的方法应该只关注如何添加地址，而不应该关注它是不是安全的或者是否需要支持事务。

图1.2展示了这种复杂性。左边的业务对象与系统级服务结合得过于紧密。每个对象不但要知道它需要记日志、进行安全控制和参与事务，还要亲自执行这些服务。

![](https://img-blog.csdnimg.cn/20191116124249668.png)

在整个系统内，关注点（例如日志和安全）的调用经常散布到各个模块中，而这些关注点并不是模块的核心业务。

**AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去**。所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性。总之，**AOP能够确保POJO的简单性**。

如图1.3所示，**我们可以把切面想象为覆盖在很多组件之上的一个外壳**。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。**这是一个非常强大的理念，可以将安全、事务和日志关注点与核心业务逻辑相分**
**离**。

![](https://img-blog.csdnimg.cn/20191116124306594.png)



### 1.1.4 使用模板消除样板式代码

你是否写过这样的代码，当编写的时候总会感觉以前曾经这么写过？我的朋友，这不是似曾相识。这是样板式的代码（boilerplate code）。通常为了实现通用的和简单的任务，你不得不一遍遍地重复编写这样的代码。

遗憾的是，它们中的很多是因为使用Java API而导致的样板式代码。样板式代码的一个常见范例是使用JDBC访问数据库查询数据。

Spring旨在通过模板封装来消除样板式代码。



## 1.2 容纳你的Bean

在基于Spring的应用中，你的应用对象生存于Spring容器（container）中。Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从生存到死亡（在这里，可能就是new到finalize()）。

**容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。毫无疑问，这些对象更简单干净，更易于理解，更易于重用并且更易于进行单元测试**。

Spring容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。bean工厂（由org.springframework. beans.factory.eanFactory接口定义）是最简单的容器，提供基本的DI支持。应用上下文
（由org.springframework.context.ApplicationContext接口定义）基于BeanFactory构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

虽然我们可以在bean工厂和应用上下文之间任选一种，但bean工厂对大多数应用来说往往太低级了，因此，应用上下文要比bean工厂更受欢迎。我们会把精力集中在应用上下文的使用上，不再浪费时间讨论bean工厂。



### 1.2.1 使用应用上下文

Spring自带了多种类型的应用上下文。下面罗列的几个是你最有可能遇到的。

- AnnotationConfigApplicationContext：从一个或多个基于Java的配置类中加载Spring应用上下文。
- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
- FileSystemXmlapplicationcontext：从文件系统下的一个或多个XML配置文件中加载上下文定义。
- XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义。



### 1.2.2 bean的生命周期

可以参考[Spring容器中bean的生命周期](https://blog.csdn.net/ThinkWon/article/details/102866432)

在传统的Java应用中，bean的生命周期很简单。使用Java关键字new进行bean实例化，然后该bean就可以使用了。一旦该bean不再被使用，则由Java自动进行垃圾回收。相比之下，Spring容器中的bean的生命周期就显得相对复杂多了。**正确理解Spring bean的生命周期非常重要，因为你或许要利用Spring提供的扩展点来自定义bean的创建过程**。下图展示了bean装载到Spring应用上下文中的一个典型的生命周期过程。

![img](https://img-blog.csdnimg.cn/201911012343410.png)

bean在Spring容器中从创建到销毁经历了若干阶段，每一阶段都可以针对Spring如何管理bean进行个性化定制。

正如你所见，在bean准备就绪之前，bean工厂执行了若干启动步骤。

我们对上图进行详细描述：

1. Spring对bean进行实例化；
2. Spring将值和bean的引用注入到bean对应的属性中；
3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBean-Name()方法；
4. 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入；
5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；
6. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessBeforeInitialization()方法；
7. 如果bean实现了InitializingBean接口，Spring将调用它们的after-PropertiesSet()方法。类似地，如果bean使用initmethod声明了初始化方法，该方法也会被调用；
8. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessAfterInitialization()方法；
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
10. 如果bean实现了DisposableBean接口，Spring将调用它的destroy()接口方法。同样，如果bean使用destroy-method声明了销毁方法，该方法也会被调用。

现在你已经了解了如何创建和加载一个Spring容器。但是一个空的容器并没有太大的价值，在你把东西放进去之前，它里面什么都没有。为了从Spring的DI(依赖注入)中受益，我们必须将应用对象装配进Spring容器中。



## 1.3 俯瞰Spring风景线

正如你所看到的，Spring框架关注于通过DI、AOP和消除样板式代码来简化企业级Java开发。即使这是Spring所能做的全部事情，那Spring也值得一用。但是，Spring实际上的功能超乎你的想象。

在Spring框架的范畴内，你会发现Spring简化Java开发的多种方式。但在Spring框架之外还存在一个构建在核心框架之上的庞大生态圈，它将Spring扩展到不同的领域，例如Web服务、REST、移动开发以及NoSQL。

首先让我们拆开Spring框架的核心来看看它究竟为我们带来了什么，然后我们再浏览下Spring Portfolio中的其他成员。

### 1.3.1 Spring模块

这里只简单介绍Spring模块，具体的可以查看[Spring模块组成(框架组成、整体架构、体系架构、体系结构)](https://blog.csdn.net/ThinkWon/article/details/102810819)

Spring 总共大约有 20 个模块， 由 1300 多个不同的文件构成。 而这些组件被分别整合在核心容器（Core Container） 、 AOP（Aspect Oriented Programming）和设备支持（Instrmentation） 、数据访问与集成（Data Access/Integeration） 、 Web、 消息（Messaging） 、 Test等 6 个模块中。 以下是 Spring 5 的模块结构图：

![](https://img-blog.csdnimg.cn/2019102923475419.png)




### 1.3.2 Spring Portfolio

当谈论Spring时，其实它远远超出我们的想象。事实上，Spring远不是Spring框架所下载的那些。如果仅仅停留在核心的Spring框架层面，我们将错过Spring Portfolio所提供的巨额财富。整个Spring Portfolio包括多个构建于核心Spring框架之上的框架和类库。概括地讲，**整个Spring Portfolio几乎为每一个领域的Java开发都提供了Spring**
**编程模型**。

或许需要几卷书才能覆盖Spring Portfolio所提供的所有内容，这也远远超出了本书的范围。不过，我们会介绍Spring Portfolio中的一些项目，同样，我们将体验一下核心框架之外的另一番风景。

Spring Portfolio主要包括如下项目，这里不一一介绍Spring Web Flow，Spring Web Service，Spring Security，Spring Integration，Spring Batch，Spring Data，Spring Social，Spring Mobile，Spring for Android，Spring Boot等，感兴趣的小伙伴可以自行研究。

**Spring Security**

安全对于许多应用都是一个非常关键的切面。利用Spring AOP，Spring Security为Spring应用提供了声明式的安全机制。



**Spring Integration**

许多企业级应用都需要与其他应用进行交互。Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。



**Spring Data**

Spring Data使得在Spring中使用任何数据库都变得非常容易。尽管关系型数据库统治企业级应用多年，但是现代化的应用正在认识到并不是所有的数据都适合放在一张表中的行和列中。一种新的数据库种类，通常被称之为NoSQL数据库，提供了使用数据的新方法，这些方法会比传统的关系型数据库更为合适。

不管你使用文档数据库，如MongoDB，图数据库，如Neo4j，还是传统的关系型数据库，Spring Data都为持久化提供了一种简单的编程模型。这包括为多种数据库类型提供了一种自动化的Repository机制，它负责为你创建Repository的实现。



**Spring Boot**

Spring极大地简化了众多的编程任务，减少甚至消除了很多样板式代码，如果没有Spring的话，在日常工作中你不得不编写这样的样板代码。**Spring Boot是一个崭新的令人兴奋的项目，它以Spring的视角，致力于简化Spring本身**。

Spring Boot大量依赖于自动配置技术，它能够消除大部分（在很多场景中，甚至是全部）Spring配置。它还提供了多个Starter项目，不管你使用Maven还是Gradle，这都能减少Spring工程构建文件的大小。



## 1.4 Spring的新功能

当本书的第3版交付印刷的时候，当时Spring的最新版本是3.0.5。那大约是在3年前，从那时到现在发生了很多的变化。Spring框架经历了3个重要的发布版本——3.1、3.2以及现在的4.0——每个版本都带来了新的特性和增强，以简化应用程序的研发。Spring Portfolio中的一些成员项目也经历了重要的变更。

本书也进行了更新，试图涵盖这些发布版本中众多最令人兴奋和有用的特性。但现在，我们先简要地了解一下Spring带来了哪些新功能。

这里就不介绍Spring 3.0的新特性了，因为Spring 5.0都出来了，旧版本的新特新就没有多大必要介绍了。

### 1.4.1 Spring 3.1新特性



### 1.4.2 Spring 3.2新特性



### 1.4.3 Spring 4.0新特性

当编写本书时，Spring 4.0是最新的发布版本。在Spring 4.0中包含了很多令人兴奋的新特性，包括：

- Spring提供了对WebSocket编程的支持，包括支持JSR-356——Java API for WebSocket；
- 鉴于WebSocket仅仅提供了一种低层次的API，急需高层次的抽象，因此Spring 4.0在WebSocket之上提供了一个高层次的面向消息的编程模型，该模型基于SockJS，并且包含了对STOMP协议的支持；
- 新的消息（messaging）模块，很多的类型来源于Spring Integration项目。这个消息模块支持Spring的SockJS/STOMP功能，同时提供了基于模板的方式发布消息；
- Spring是第一批（如果不说是第一个的话）支持Java 8特性的Java框架，比如它所支持的lambda表达式。别的暂且不说，这首先能够让使用特定的回调接口（如RowMapper和JdbcTemplate）更加简洁，代码更加易读；
- 与Java 8同时得到支持的是JSR-310——Date与Time API，在处理日期和时间时，它为开发者提供了比java.util.Date或java.util.Calendar更丰富的API；
- 为Groovy开发的应用程序提供了更加顺畅的编程体验，尤其是支持非常便利地完全采用Groovy开发Spring应用程序。随这些一起提供的是来自于Grails的BeanBuilder，借助它能够通过Groovy配置Spring应用；
- 添加了条件化创建bean的功能，在这里只有开发人员定义的条件满足时，才会创建所声明的bean；
- Spring 4.0包含了Spring RestTemplate的一个新的异步实现，它会立即返回并且允许在操作完成后执行回调；
- 添加了对多项JEE规范的支持，包括JMS 2.0、JTA 1.2、JPA 2.1和Bean Validation 1.1。



### 1.4.4 Spring 5.0新特性

Spring 5.0是在2013年发布Spring 4后的第一个大版本，5.0 M1在2016年7月28日发布。随着慢慢的推广，使用它的人数肯定也会越来越多，那么Spring 5有哪些新的特性呢？一起来看下吧

基本可以归为如下几类：

- JDK版本升级

- Core框架修订，核心容器更新

- Kotlin函数式编程

- 响应式编程模型

- 测试改进

- 额外库支持

- 停止维护一些特性


可以看到，在Spring框架的最新发布版本中，包含了很多令人兴奋的新特性。在本书中，我们将会看到很多这样的新特性，同时也会学习Spring中长期以来一直存在的特性。



## 1.5 小结

现在，你应该对Spring的功能特性有了一个清晰的认识。**Spring致力于简化企业级Java开发，促进代码的松散耦合。成功的关键在于依赖注入和AOP**。

在本章，我们先体验了Spring的DI。DI是组装应用对象的一种方式，借助这种方式对象无需知道依赖来自何处或者依赖的实现方式。不同于自己获取依赖对象，对象会在运行期赋予它们所依赖的对象。依赖对象通常会通过接口了解所注入的对象，这样的话就能确保低耦合。除了DI，我们还简单介绍了Spring对AOP的支持。AOP可以帮助应用
将散落在各处的逻辑汇集于一处——切面。当Spring装配bean的时候，这些切面能够在运行期编织起来，这样就能非常有效地赋予bean新的行为。

**依赖注入和AOP是Spring框架最核心的部分**，因此只有理解了如何应用Spring最关键的功能，你才有能力使用Spring框架的其他功能。在本章，我们只是触及了Spring DI和AOP特性的皮毛。在以后的几章，我们将深入探讨DI和AOP。



