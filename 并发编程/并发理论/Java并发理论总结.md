# Java并发理论总结

在多线程中稍微不注意就会出现线程安全问题，那么什么是线程安全问题？为什么会出现线程安全问题？出现线程安全的问题一般是因为**主内存和工作内存数据不一致性**和**重排序**导致的，而解决线程安全的问题最重要的就是理解这两种问题是怎么来的，那么，理解它们的核心在于理解[Java内存模型（JMM）](https://blog.csdn.net/ThinkWon/article/details/102073578)。

为了性能优化，还会涉及到[重排序与数据依赖性](https://blog.csdn.net/ThinkWon/article/details/102073858)。

为了在不改变程序执行结果的前提下，尽可能地提高程序执行的并行度，我们需要了解[as-if-serial规则和happens-before规则的区别](https://blog.csdn.net/ThinkWon/article/details/102074107)



从[Java内存模型（JMM）](https://blog.csdn.net/ThinkWon/article/details/102073578)聊了关于JMM的两个方面：1. JMM的抽象结构（主内存和线程工作内存）；2. 重排序以及happens-before规则。接下来，我们来做一个总结。从三个方面进行考虑。1. 如果让我们设计JMM应该从哪些方面考虑，也就是说JMM承担哪些功能；2. happens-before与JMM的关系；3. 由于JMM，多线程情况下可能会出现哪些问题？

[toc]



## JMM的设计 ##

![JMM层级图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlQjklQjYlRTUlOEYlOTElRTclQkMlOTYlRTclQTglOEItJUU1JUI5JUI2JUU1JThGJTkxJUU3JTkwJTg2JUU4JUFFJUJBL0pNTSVFNSVCMSU4MiVFNyVCQSVBNyVFNSU5QiVCRS5wbmc)

JMM是语言级的内存模型，在我的理解中JMM处于中间层，包含了两个方面：（1）内存模型；（2）重排序以及happens-before规则。同时，为了禁止特定类型的重排序会对编译器和处理器指令序列加以控制。而上层会有基于JMM的关键字和J.U.C包下的一些具体类用来方便程序员能够迅速高效率的进行并发编程。站在JMM设计者的角度，在设计JMM时需要考虑两个关键因素:

1. **程序员对内存模型的使用**
程序员希望内存模型易于理解、易于编程。程序员希望基于一个强内存模型来编写代码。
2. **编译器和处理器对内存模型的实现**
编译器和处理器希望内存模型对它们的束缚越少越好，这样它们就可以做尽可能多的优化来提高性能。编译器和处理器希望实现一个弱内存模型。

另外还要一个特别有意思的事情就是关于重排序问题，更简单的说，重排序可以分为两类：

1. 会改变程序执行结果的重排序。
2. 不会改变程序执行结果的重排序。

JMM对这两种不同性质的重排序，采取了不同的策略，如下。

1. 对于会改变程序执行结果的重排序，JMM要求编译器和处理器必须禁止这种重排序。
2. 对于不会改变程序执行结果的重排序，JMM对编译器和处理器不做要求（JMM允许这种
重排序）



JMM的设计图为：

![JMM设计示意图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlQjklQjYlRTUlOEYlOTElRTclQkMlOTYlRTclQTglOEItJUU1JUI5JUI2JUU1JThGJTkxJUU3JTkwJTg2JUU4JUFFJUJBL0pNTSVFOCVBRSVCRSVFOCVBRSVBMSVFNyVBNCVCQSVFNiU4NCU4RiVFNSU5QiVCRS5wbmc)
从图可以看出：

1. JMM向程序员提供的happens-before规则能满足程序员的需求。JMM的happens-before规则不但简单易懂，而且也向程序员提供了足够强的内存可见性保证（有些内存可见性保证其实并不一定真实存在）。
2. JMM对编译器和处理器的束缚已经尽可能少。从上面的分析可以看出，JMM其实是在遵循一个基本原则：只要不改变程序的执行结果（指的是单线程程序和正确同步的多线程程序），编译器和处理器怎么优化都行。例如，如果编译器经过细致的分析后，认定一个锁只会被单个线程访问，那么这个锁可以被消除。再如，如果编译器经过细致的分析后，认定一个volatile变量只会被单个线程访问，那么编译器可以把这个volatile变量当作一个普通变量来对待。这些优化既不会改变程序的执行结果，又能提高程序的执行效率。



## happens-before与JMM的关系 ##


![happens-before与JMM的关系](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTUlQjklQjYlRTUlOEYlOTElRTclQkMlOTYlRTclQTglOEItJUU1JUI5JUI2JUU1JThGJTkxJUU3JTkwJTg2JUU4JUFFJUJBL2hhcHBlbnMtYmVmb3JlJUU0JUI4JThFSk1NJUU3JTlBJTg0JUU1JTg1JUIzJUU3JUIzJUJCLnBuZw)

一个happens-before规则对应于一个或多个编译器和处理器重排序规则。对于Java程序员来说，happens-before规则简单易懂，它避免Java程序员为了理解JMM提供的内存可见性保证而去学习复杂的重排序规则以及这些规则的具体实现方法



## 需要关注的问题 ##

从内存抽象结构来说，可能出在数据“脏读”的现象，这就是**数据可见性**的问题，另外，重排序在多线程中不注意的话也容易存在一些问题，比如一个很经典的问题就是DCL（双重检验锁），这就是需要**禁止重排序**，另外，在多线程下原子操作，例如i++不加以注意的也容易出现线程安全的问题。但总的来说，在多线程开发时需要从**原子性，有序性，可见性**三个方面进行考虑。J.U.C包下的并发工具类和并发容器也是需要花时间去掌握的，这些东西在以后得文章中多会一一进行讨论。


> 参考文献 《Java并发编程的艺术》
