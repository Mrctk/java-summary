# 控制反转(IoC)与依赖注入(DI)详解

学习了Spring框架的小伙伴们都知道Spring框架的核心是IoC容器和AOP模块。IoC容器是Spring框架的底层基础，用于管理对象的创建和对象依赖关系的维护。我们有必要好好学习IoC。

我们带着下面的4个问题来学习本博客

1. 什么是控制反转(IoC)
2. 控制反转(IoC)有什么作用
3. 控制反转(IoC)是怎么分类的
4. 什么是依赖注入(DI)



## 什么是控制反转(IoC)

控制反转即IoC (Inversion of Control)，它把传统上由程序代码直接操控的对象的调用权交给容器，通过容器来实现对象组件的装配和管理。所谓的“控制反转”概念就是对组件对象控制权的转移，从程序代码本身转移到了外部容器。



## 控制反转(IoC)有什么作用

- 管理对象的创建和依赖关系的维护。对象的创建并不是一件简单的事，在对象关系比较复杂时，如果依赖关系需要程序猿来维护的话，那是相当头疼的。
- 解耦，由容器去维护具体的对象

- 托管了类的产生过程，比如我们需要在类的产生过程中做一些处理，最直接的例子就是代理，如果有容器程序可以把这部分处理交给容器，应用程序则无需去关心类是如何完成代理的



## 控制反转(IoC)是怎么分类的

IoC是一个很大的概念，可以用不同的方式来实现。其主要实现方式有两种：依赖注入和依赖查找

### 依赖注入

依赖注入（Dependency Injection）：组件不做定位查询，只提供普通的Java方法让容器去决定依赖关系。Spring框架使用这种方式。依赖注入是时下最流行的IoC实现方式，依赖注入分为接口注入（Interface Injection），Setter方法注入（Setter Injection）和构造器注入（Constructor Injection）三种方式。其中接口注入由于在灵活性和易用性比较差，现在从Spring4开始已被废弃。 

依赖注入之所以更流行是因为它是一种更可取的方式：让容器全权负责依赖查询，受管组件只需要暴露JavaBean的setter方法或者带参数的构造器或者接口，使容器可以在初始化时组装对象的依赖关系。其与依赖查找方式相比，主要优势为：

- 查找定位操作与应用代码完全无关。
- 不依赖于容器的API，可以很容易地在任何容器以外使用应用对象。
- 不需要特殊的接口，绝大多数对象可以做到完全不必依赖容器。



接口注入代码：

```java
package com.tyyd.dao;

/**

 *  DAO接口
    */
    public interface IndexDao {
    void createIndexDao(IndexDao indexDao);
    }

```

```java
    package com.tyyd.service.impl;

import com.tyyd.dao.IndexDao;
import com.tyyd.service.IndexService;

/**

 * 测试Sercice实现类
   */
   public class IndexServiceImpl implements IndexService, IndexDao {

   private IndexDao indexDao;

   /**

    * 测试方法
      */
      public void test() {
      indexDao.test();
      }

   public void createIndexDao(IndexDao indexDao) {
       this.indexDao = indexDao
   }
   }
```





设置注入代码：

```java
package com.tyyd.service.impl;

import com.tyyd.dao.IndexDao;
import com.tyyd.service.IndexService;

/**

 * 测试Sercice实现类
   */
   public class IndexServiceImpl implements IndexService {

   private IndexDao indexDao;

   /**

    * 测试方法
      */
      public void test() {
      indexDao.test();
      }

   /**

    * IOC Set方法注入
    * @param indexDao
      */
      public void setIndexDao(IndexDao indexDao) {
      this.indexDao = indexDao;
      }
      }
```



构造方法注入代码：

```java
package com.tyyd.service.impl;

import com.tyyd.dao.IndexDao;
import com.tyyd.service.IndexService;

/**

 * 测试Sercice实现类
   */
   public class IndexServiceImpl implements IndexService {

   @Autowired
   private IndexDao indexDao;

   /**

    * IOC 构造方法注入
    * @param indexDao
      */
      public IndexServiceImpl(IndexDao indexDao) {
      this.indexDao = indexDao;
      }

   /**

    * 测试方法
      */
      public void test() {
      indexDao.test();
      }
      }
```









### 依赖查找

依赖查找（Dependency Lookup）：容器提供回调接口和上下文环境给组件。EJB和Apache Avalon都使用这种方式。

依赖查找也有两种类型：依赖拖拽（DP）和上下文化依赖查找(CDL)。

```java
public class PigServiceImpl {
    private DataSource dataSource;
    private PigDao pigDao;
public PigServiceImpl(){
    Context context = null;
    try{
        context = new InitialContext();
        dataSource = (DataSource)context.lookup(“java:comp/env/dataSourceName”);
        pigDao = (PigDao) context.lookup(“java:comp/env/PigDaoName”);
    } catch (Exception e) {
    
    }
}
}
```





#### 上下文依赖查找(Contextualized Dependency Lookup) 

在某些方面跟依赖拖拽类似，但是上下文依赖查找中，查找的过程是在容器管理的资源中进行的，而不是从集中注册表中，并且通常是作用在某些设置点上。



```java
public class PigServiceImpl {
    private DataSource dataSource;
    private PigDao pigDao;
public PigServiceImpl(){
    Context context = null;
    try{
        context = new InitialContext();
        dataSource = (DataSource)context.lookup(“java:comp/env/dataSourceName”);
        pigDao = (PigDao) context.lookup(“java:comp/env/PigDaoName”);
    } catch (Exception e) {
    
    }
}
}
```




#### 依赖拖拽 (Dependency Pull)

依赖拖拽：注入的对象如何与组件发生联系，这个过程就是通过依赖拖拽实现 。(较少有使用)

而通常对注入对象的配置可以通过一个 xml 文件完成。

依赖拖拽这种方式对对象进行集中管理。

```java
package com.tyyd.ioc.test;

import com.tyyd.ioc.annotation.service.impl.PigServiceImpl;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("classpath:spring-ioc.xml");
        PigServiceImpl pigService = (PigServiceImpl)classPathXmlApplicationContext.getBean("pigServiceImpl");
        pigService.test();
    }
}
```



通常我们对注入对象的配置可以通过一个xml文件完成。并使用这种方式对对象进行集中管理，使用依赖拖拽与依赖查找本质的区别是，依赖查找是在业务组件代码中进行的，而不是从一个集中的注册处，特定的地点执行。


