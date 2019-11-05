# 业务复杂=if else？刚来的大神竟然用策略+工厂彻底干掉了他们！

![](https://img-blog.csdnimg.cn/20191105212234795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aGlua3dvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)



[toc]



## 太多if-else影响可读性和可维护性

对于业务开发来说，业务逻辑的复杂是必然的，随着业务发展，需求只会越来越复杂，为了考虑到各种各样的情况，代码中不可避免的会出现很多if-else。

一旦代码中if-else过多，就会大大的影响其可读性和可维护性。

![](https://img-blog.csdnimg.cn/20191105212253832.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aGlua3dvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

- 首先可读性，不言而喻，过多的if-else代码和嵌套，会使阅读代码的人很难理解到底是什么意思。尤其是那些没有注释的代码。

- 其次是可维护性，因为if-else特别多，想要新加一个分支的时候，就会很难添加，极其容易影响到其他的分支。


笔者曾经看到过一个支付的核心应用，这个应用支持了很多业务的线上支付功能，但是每个业务都有很多定制的需求，所以很多核心的代码中都有一大坨if-else。

每个新业务需要定制的时候，都把自己的if放到整个方法的最前面，以保证自己的逻辑可以正常执行。这种做法，后果可想而知。

其实，**if-else是有办法可以消除掉的，其中比较典型的并且使用广泛的就是借助策略模式和工厂模式，准确的说是利用这两个设计模式的思想，彻底消灭代码中的if-else。**



## 举例外卖平台需求

本文，就结合这两种设计模式，介绍如何消除if-else，并且，还会介绍如何和Spring框架结合，这样读者看完本文之后就可以立即应用到自己的项目中。

本文涉及到一些代码，但是作者尽量用通俗的例子和伪代码等形式使内容不那么枯燥。

恶心的if-else

假设我们要做一个外卖平台，有这样的需求：

> 1、外卖平台上的某家店铺为了促销，设置了多种会员优惠，其中包含超级会员折扣8折、普通会员折扣9折和普通用户没有折扣三种。
>
> 2、希望用户在付款的时候，根据用户的会员等级，就可以知道用户符合哪种折扣策略，进而进行打折，计算出应付金额。
>
> 3、随着业务发展，新的需求要求专属会员要在店铺下单金额大于30元的时候才可以享受优惠。
>
> 4、接着，又有一个变态的需求，如果用户的超级会员已经到期了，并且到期时间在一周内，那么就对用户的单笔订单按照超级会员进行折扣，并在收银台进行强提醒，引导用户再次开通会员，而且折扣只进行一次。

那么，我们可以看到以下伪代码：

```java
public BigDecimal calPrice(BigDecimal orderPrice, String buyerType) {

    if (用户是专属会员) {
        if (订单金额大于30元) {
            returen 7折价格;
        }
    }

    if (用户是超级会员) {
        return 8折价格;
    }

    if (用户是普通会员) {
        if(该用户超级会员刚过期并且尚未使用过临时折扣){
            临时折扣使用次数更新();
            returen 8折价格;
        }
        return 9折价格;
    }
    return 原价;
}
```

以上，就是对于这个需求的一段价格计算逻辑，使用伪代码都这么复杂，如果是真的写代码，那复杂度可想而知。

这样的代码中，有很多if-else，并且还有很多的if-else的嵌套，无论是可读性还是可维护性都非常低。

那么，如何改善呢？



## 引入策略模式

策略模式

接下来，我们尝试引入策略模式来提升代码的可维护性和可读性。

首先，定义一个接口：

```java
/**
 * @author mhcoding
 */
public interface UserPayService {

    /**
     * 计算应付价格
     */
    public BigDecimal quote(BigDecimal orderPrice);
}
```



接着定义几个策略类：

```java
/**
 * @author mhcoding
 */
public class ParticularlyVipPayService implements UserPayService {

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
         if (消费金额大于30元) {
            return 7折价格;
        }
    }
}

public class SuperVipPayService implements UserPayService {

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
        return 8折价格;
    }
}

public class VipPayService implements UserPayService {

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
        if(该用户超级会员刚过期并且尚未使用过临时折扣){
            临时折扣使用次数更新();
            returen 8折价格;
        }
        return 9折价格;
    }
}
```



引入了策略之后，我们可以按照如下方式进行价格计算：

```java
/**
 * @author mhcoding
 */
public class Test {

    public static void main(String[] args) {
        UserPayService strategy = new VipPayService();
        BigDecimal quote = strategy.quote(300);
        System.out.println("普通会员商品的最终价格为：" + quote.doubleValue());

        strategy = new SuperVipPayService();
        quote = strategy.quote(300);
        System.out.println("超级会员商品的最终价格为：" + quote.doubleValue());
    }
}
```



以上，就是一个例子，可以在代码中new出不同的会员的策略类，然后执行对应的计算价格的方法。这个例子以及策略模式的相关知识，读者可以在《[**如何给女朋友解释什么是策略模式？**](http://mp.weixin.qq.com/s?__biz=Mzg3MjA4MTExMw==&mid=2247485532&idx=1&sn=d915794a429ca24875eb6343d9322787&chksm=cef5fbeaf98272fcfb80075078b4419ee7f9d703eee5aa79c2b50a31248ed2905748224ce1a9&scene=21#wechat_redirect)》一文中学习。

但是，真正在代码中使用，比如在一个web项目中使用，上面这个Demo根本没办法直接用。

- 首先，在web项目中，上面我们创建出来的这些策略类都是被Spring托管的，我们不会自己去new一个实例出来。

- 其次，在web项目中，如果真要计算价格，也是要事先知道用户的会员等级，比如从数据库中查出会员等级，然后根据等级获取不同的策略类执行计算价格方法。


那么，web项目中真正的计算价格的话，伪代码应该是这样的：

```java
/**
 * @author mhcoding
 */
public BigDecimal calPrice(BigDecimal orderPrice,User user) {

     String vipType = user.getVipType();

     if (vipType == 专属会员) {
        //伪代码：从Spring中获取超级会员的策略对象
        UserPayService strategy = Spring.getBean(ParticularlyVipPayService.class);
        return strategy.quote(orderPrice);
     }

     if (vipType == 超级会员) {
        UserPayService strategy = Spring.getBean(SuperVipPayService.class);
        return strategy.quote(orderPrice);
     }

     if (vipType == 普通会员) {
        UserPayService strategy = Spring.getBean(VipPayService.class);
        return strategy.quote(orderPrice);
     }
     return 原价;
}
```



通过以上代码，我们发现，代码可维护性和可读性好像是好了一些，但是好像并没有减少if-else啊。

其实，在之前的《[**如何给女朋友解释什么是策略模式？**](http://mp.weixin.qq.com/s?__biz=Mzg3MjA4MTExMw==&mid=2247485532&idx=1&sn=d915794a429ca24875eb6343d9322787&chksm=cef5fbeaf98272fcfb80075078b4419ee7f9d703eee5aa79c2b50a31248ed2905748224ce1a9&scene=21#wechat_redirect)》一文中，我们介绍了很多策略模式的优点。但是，策略模式的使用上，还是有一个比较大的缺点的：

> 客户端必须知道所有的策略类，并自行决定使用哪一个策略类。这就意味着客户端必须理解这些算法的区别，以便适时选择恰当的算法类。

也就是说，虽然在计算价格的时候没有if-else了，但是选择具体的策略的时候还是不可避免的还是要有一些if-else。

另外，上面的伪代码中，从Spring中获取会员的策略对象我们是伪代码实现的，那么代码到底该如何获取对应的Bean呢？



## 借助Spring和工厂模式解决策略模式存在的问题

接下来我们看如何借助Spring和工厂模式，解决上面这些问题。

工厂模式

为了方便我们从Spring中获取UserPayService的各个策略类，我们创建一个工厂类：

```java
/**
 * @author mhcoding
 */
public class UserPayServiceStrategyFactory {

    private static Map<String,UserPayService> services = new ConcurrentHashMap<String,UserPayService>();

    public  static UserPayService getByUserType(String type){
        return services.get(type);
    }

    public static void register(String userType,UserPayService userPayService){
        Assert.notNull(userType,"userType can't be null");
        services.put(userType,userPayService);
    }
}
```

这个UserPayServiceStrategyFactory中定义了一个Map，用来保存所有的策略类的实例，并提供一个getByUserType方法，可以根据类型直接获取对应的类的实例。还有一个register方法，这个后面再讲。

有了这个工厂类之后，计算价格的代码即可得到大大的优化：

```java
/**
 * @author mhcoding
 */
public BigDecimal calPrice(BigDecimal orderPrice,User user) {

     String vipType = user.getVipType();
     UserPayService strategy = UserPayServiceStrategyFactory.getByUserType(vipType);
     return strategy.quote(orderPrice);
}
```

以上代码中，不再需要if-else了，拿到用户的vip类型之后，直接通过工厂的getByUserType方法直接调用就可以了。

**通过策略+工厂，我们的代码很大程度的优化了，大大提升了可读性和可维护性**。

但是，上面还遗留了一个问题，那就是UserPayServiceStrategyFactory中用来保存所有的策略类的实例的Map是如何被初始化的？各个策略的实例对象如何塞进去的呢？



## Spring Bean的注册

Spring Bean的注册

还记得我们前面定义的UserPayServiceStrategyFactory中提供了的register方法吗？他就是用来注册策略服务的。

接下来，我们就想办法调用register方法，把Spring通过IOC创建出来的Bean注册进去就行了。

这种需求，可以借用Spring种提供的InitializingBean接口，这个接口为Bean提供了属性初始化后的处理方法，它只包括afterPropertiesSet方法，凡是继承该接口的类，在bean的属性初始化后都会执行该方法。

那么，我们将前面的各个策略类稍作改造即可：

```java
/**
 * @author mhcoding
 */
@Service
public class ParticularlyVipPayService implements UserPayService,InitializingBean {

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
         if (消费金额大于30元) {
            return 7折价格;
        }
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        UserPayServiceStrategyFactory.register("ParticularlyVip",this);
    }
}

@Service
public class SuperVipPayService implements UserPayService ,InitializingBean{

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
        return 8折价格;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        UserPayServiceStrategyFactory.register("SuperVip",this);
    }
}

@Service  
public class VipPayService implements UserPayService,InitializingBean {

    @Override
    public BigDecimal quote(BigDecimal orderPrice) {
        if(该用户超级会员刚过期并且尚未使用过临时折扣){
            临时折扣使用次数更新();
            returen 8折价格;
        }
        return 9折价格;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        UserPayServiceStrategyFactory.register("Vip",this);
    }
}
```

只需要每一个策略服务的实现类都实现InitializingBean接口，并实现其afterPropertiesSet方法，在这个方法中调用UserPayServiceStrategyFactory.register即可。

这样，在Spring初始化的时候，当创建VipPayService、SuperVipPayService和ParticularlyVipPayService的时候，会在Bean的属性初始化之后，把这个Bean注册到UserPayServiceStrategyFactory中。

以上代码，其实还是有一些重复代码的，这里面还可以引入模板方法模式进一步精简，这里就不展开了。

还有就是，UserPayServiceStrategyFactory.register调用的时候，第一个参数需要传一个字符串，这里的话其实也可以优化掉。比如使用枚举，或者在每个策略类中自定义一个getUserType方法，各自实现即可。



## 总结

本文，我们通过策略模式、工厂模式以及Spring的InitializingBean，提升了代码的可读性以及可维护性，彻底消灭了一坨if-else。

文中的这种做法，大家可以立刻尝试起来，这种实践，是我们日常开发中经常用到的，而且还有很多衍生的用法，也都非常好用。有机会后面再介绍。

其实，如果读者们对策略模式和工厂模式了解的话，文中使用的并不是严格意义上面的策略模式和工厂模式。

首先，策略模式中重要的Context角色在这里面是没有的，没有Context，也就没有用到组合的方式，而是使用工厂代替了。

另外，这里面的UserPayServiceStrategyFactory其实只是维护了一个Map，并提供了register和get方法而已，而工厂模式其实是帮忙创建对象的，这里并没有用到。

所以，读者不必纠结于到底是不是真的用了策略模式和工厂模式。而且，这里面也再扩展一句，所谓的GOF 23种设计模式，无论从哪本书或者哪个博客看，都是简单的代码示例，但是我们日常开发很多都是基于Spring等框架的，根本没办法直接用的。

所以，对于设计模式的学习，重要的是学习其思想，而不是代码实现！！！

如果读者们感兴趣，后续可以出更多的设计模式和Spring等框架结合使用的最佳实践。希望通过这样的文章，读者可以真正的在代码中使用上设计模式。


