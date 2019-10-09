# Java内存模型

Java 内存模型试图屏蔽各种硬件和操作系统的内存访问差异，以实现让 Java 程序在各种平台下都能达到一致的内存访问效果。

[TOC]

## Java内存模型（JMM）的介绍

什么是线程安全？在<<深入理解Java虚拟机>>中看到的定义。原文如下：
当多个线程访问同一个对象时，如果不用考虑这些线程在运行时环境下的调度和交替运行，也不需要进行额外的同步，或者在调用方进行任何其他的协调操作，调用这个对象的行为都可以获取正确的结果，那这个对象是线程安全的。

关于定义的理解是一个仁者见仁智者见智的事情。出现线程安全的问题一般是因为**主内存和工作内存数据不一致性**和**重排序**导致的，而解决线程安全的问题最重要的就是理解这两种问题是怎么来的，那么，理解它们的核心在于理解Java内存模型（JMM）。

在多线程条件下，多个线程肯定会相互协作完成一件事情，一般来说就会涉及到**多个线程间相互通信告知彼此的状态以及当前的执行结果**等，另外，为了性能优化，还会**涉及到编译器指令重排序和处理器指令重排序**。下面会一一来聊聊这些知识。



## 内存模型抽象结构

线程间协作通信可以类比人与人之间的协作的方式，在现实生活中，之前网上有个流行语“你妈喊你回家吃饭了”，就以这个生活场景为例，小明在外面玩耍，小明妈妈在家里做饭，做完饭后准备叫小明回家吃饭，那么就存在两种方式：

小明妈妈要去上班了十分紧急这个时候手机又没有电了，于是就在桌子上贴了一张纸条“饭做好了，放在...”小明回家后看到纸条如愿吃到妈妈做的饭菜，那么，如果将小明妈妈和小明作为两个线程，那么这张纸条就是这两个线程间通信的共享变量，通过读写共享变量实现两个线程间协作；

还有一种方式就是，妈妈的手机还有电，妈妈在赶去坐公交的路上给小明打了个电话，这种方式就是通知机制来完成协作。同样，可以引申到线程间通信机制。

通过上面这个例子，应该有些认识。在并发编程中主要需要解决两个问题：**1. 线程之间如何通信；2.线程之间如何完成同步**（这里的线程指的是并发执行的活动实体）。**通信是指线程之间以何种机制来交换信息，主要有两种：共享内存和消息传递**。这里，可以分别类比上面的两个举例。**Java内存模型是共享内存的并发模型，线程之间主要通过读-写共享变量来完成隐式通信**。如果程序员不能理解Java的共享内存模型在编写并发程序时一定会遇到各种各样关于内存可见性的问题。



### 哪些是共享变量

在Java程序中所有**实例域，静态域和数组元素**都是放在堆内存中（所有线程均可访问到，是可以共享的），而局部变量，方法定义参数和异常处理器参数不会在线程间共享。共享数据会出现线程安全的问题，而非共享数据不会出现线程安全的问题。关于JVM运行时内存区域在后面的文章会讲到。



### JMM抽象结构模型

我们知道CPU的处理速度和主存的读写速度不是一个量级的（CPU的处理速度快很多），为了平衡这种巨大的差距，每个CPU都会有缓存。因此，共享变量会先放在主存中，每个线程都有属于自己的工作内存，并且会把位于主存中的共享变量拷贝到自己的工作内存，之后的读写操作均使用位于工作内存的变量副本，并在某个时刻将工作内存的变量副本写回到主存中去。JMM就从抽象层次定义了这种方式，并且JMM决定了一个线程对共享变量的写入何时对其他线程是可见的。

![JMM内存模型的抽象结构示意图](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/JMM内存结构抽象结构示意图.png)

如图为JMM抽象示意图，线程A和线程B之间要完成通信的话，要经历如下两步：

1. 线程A从主内存中将共享变量读入线程A的工作内存后并进行操作，之后将数据重新写回到主内存中；
2. 线程B从主存中读取最新的共享变量

从横向去看看，线程A和线程B就好像通过共享变量在进行隐式通信。这其中有个意思的问题，如果线程A更新后数据并没有及时写回到主存，而此时线程B读到的是过期的数据，这就出现了“**脏读**”现象。可以通过**同步机制**（控制不同线程间操作发生的相对顺序）来解决或者通过**volatile关键字**使得每次volatile变量都能够强制刷新到主存，从而对每个线程都是可见的。







## 主内存与工作内存

处理器上的寄存器的读写的速度比内存快几个数量级，为了解决这种速度矛盾，在它们之间加入了高速缓存。

加入高速缓存带来了一个新的问题：缓存一致性。如果多个缓存共享同一块主内存区域，那么多个缓存的数据可能会不一致，需要一些协议来解决这个问题。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/942ca0d2-9d5c-45a4-89cb-5fd89b61913f.png)

所有的变量都存储在主内存中，每个线程还有自己的工作内存，工作内存存储在高速缓存或者寄存器中，保存了该线程使用的变量的主内存副本拷贝。

线程只能直接操作工作内存中的变量，不同线程之间的变量值传递需要通过主内存来完成。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/15851555-5abc-497d-ad34-efed10f43a6b.png)



## 内存间交互操作

Java 内存模型定义了 8 个操作来完成主内存和工作内存的交互操作。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/8b7ebbad-9604-4375-84e3-f412099d170c.png)

1. lock（锁定）：作用于主内存中的变量，它把一个变量标识为一个线程独占的状态；
2. unlock（解锁）：作用于主内存中的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定
3. read（读取）：作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便后面的load动作使用；
4. load（载入）：作用于工作内存中的变量，它把read操作从主内存中得到的变量值放入工作内存中的变量副本
5. use（使用）：作用于工作内存中的变量，它把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用到变量的值的字节码指令时将会执行这个操作；
6. assign（赋值）：作用于工作内存中的变量，它把一个从执行引擎接收到的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作；
7. store（存储）：作用于工作内存的变量，它把工作内存中一个变量的值传送给主内存中以便随后的write操作使用；
8. write（操作）：作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。



## 内存模型三大特性

### 1. 原子性

Java 内存模型保证了 read、load、use、assign、store、write、lock 和 unlock 操作具有原子性，例如对一个 int 类型的变量执行 assign 赋值操作，这个操作就是原子性的。但是 Java 内存模型允许虚拟机将没有被 volatile 修饰的 64 位数据（long，double）的读写操作划分为两次 32 位的操作来进行，即 load、store、read 和 write 操作可以不具备原子性。

有一个错误认识就是，int 等原子性的类型在多线程环境中不会出现线程安全问题。前面的线程不安全示例代码中，cnt 属于 int 类型变量，1000 个线程对它进行自增操作之后，得到的值为 997 而不是 1000。

为了方便讨论，将内存间的交互操作简化为 3 个：load、assign、store。

下图演示了两个线程同时对 cnt 进行操作，load、assign、store 这一系列操作整体上看不具备原子性，那么在 T1 修改 cnt 并且还没有将修改后的值写入主内存，T2 依然可以读入旧值。可以看出，这两个线程虽然执行了两次自增运算，但是主内存中 cnt 的值最后为 1 而不是 2。因此对 int 类型读写操作满足原子性只是说明 load、assign、store 这些单个操作具备原子性。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/2797a609-68db-4d7b-8701-41ac9a34b14f.jpg)

AtomicInteger 能保证多个线程修改的原子性。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/dd563037-fcaa-4bd8-83b6-b39d93a12c77.jpg)

使用 AtomicInteger 重写之前线程不安全的代码之后得到以下线程安全实现：

```java
public class AtomicExample {
    private AtomicInteger cnt = new AtomicInteger();

    public void add() {
        cnt.incrementAndGet();
    }

    public int get() {
        return cnt.get();
    }
}
```

```java
public static void main(String[] args) throws InterruptedException {
    final int threadSize = 1000;
    AtomicExample example = new AtomicExample(); // 只修改这条语句
    final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < threadSize; i++) {
        executorService.execute(() -> {
            example.add();
            countDownLatch.countDown();
        });
    }
    countDownLatch.await();
    executorService.shutdown();
    System.out.println(example.get());
}
```

输出结果
```java
1000
```



> synchronized关键字

除了使用原子类之外，也可以使用 **synchronized** 互斥锁来保证操作的原子性。它对应的内存间交互操作为：lock 和 unlock，在虚拟机实现上对应的字节码指令为 monitorenter 和 monitorexit。

```java
public class AtomicSynchronizedExample {
    private int cnt = 0;

    public synchronized void add() {
        cnt++;
    }

    public synchronized int get() {
        return cnt;
    }
}
```

```java
public static void main(String[] args) throws InterruptedException {
    final int threadSize = 1000;
    AtomicSynchronizedExample example = new AtomicSynchronizedExample();
    final CountDownLatch countDownLatch = new CountDownLatch(threadSize);
    ExecutorService executorService = Executors.newCachedThreadPool();
    for (int i = 0; i < threadSize; i++) {
        executorService.execute(() -> {
            example.add();
            countDownLatch.countDown();
        });
    }
    countDownLatch.await();
    executorService.shutdown();
    System.out.println(example.get());
}
```

输出结果
```java
1000
```



> volatile关键字

```
public class VolatileExample {
    private static volatile int counter = 0;

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            Thread thread = new Thread(new Runnable() {
                @Override
                public void run() {
                    for (int i = 0; i < 10000; i++)
                        counter++;
                }
            });
            thread.start();
        }
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(counter);
    }
}
```

开启10个线程，每个线程都自加10000次，如果不出现线程安全的问题最终的结果应该就是：10*10000 = 100000;可是运行多次都是小于100000的结果，问题在于 **volatile并不能保证原子性**，在前面说过counter++这并不是一个原子操作，包含了三个步骤：1.读取变量counter的值；2.对counter加一；3.将新值赋值给变量counter。如果线程A读取counter到工作内存后，其他线程对这个值已经做了自增操作后，那么线程A的这个值自然而然就是一个过期的值，因此，总结果必然会是小于100000的。

如果让volatile保证原子性，必须符合以下两条规则：

1. **运算结果并不依赖于变量的当前值，或者能够确保只有一个线程修改变量的值；**
2. **变量不需要与其他的状态变量共同参与不变约束**



### 2. 可见性

可见性指当一个线程修改了共享变量的值，其它线程能够立即得知这个修改。Java 内存模型是通过在变量修改后将新值同步回主内存，在变量读取前从主内存刷新变量值来实现可见性的。

主要有三种实现可见性的方式：

- volatile，通过在指令中添加**lock指令**，以实现内存可见性。
- synchronized，当线程获取锁时会从主内存中获取共享变量的最新值，释放锁的时候会将共享变量同步到主内存中。
- final，被 final 关键字修饰的字段在构造器中一旦初始化完成，并且没有发生 this 逃逸（其它线程通过 this 引用访问到初始化了一半的对象），那么其它线程就能看见 final 字段的值。

对前面的线程不安全示例中的 cnt 变量使用 volatile 修饰，不能解决线程不安全问题，因为 volatile 并不能保证操作的原子性。



### 3. 有序性

有序性是指：在本线程内观察，所有操作都是有序的。在一个线程观察另一个线程，所有操作都是无序的，无序是因为发生了指令重排序。在 Java 内存模型中，允许编译器和处理器对指令进行重排序，重排序过程不会影响到单线程程序的执行，却会影响到多线程并发执行的正确性。

volatile 关键字通过添加内存屏障的方式来禁止指令重排，即重排序时不能把后面的指令放到内存屏障之前。

synchronized 关键字同样可以保证有序性，它保证每个时刻只有一个线程执行同步代码，相当于是让线程顺序执行同步代码。



### 总结

**synchronized：具有原子性，有序性和可见性**；

**volatile：具有有序性和可见性**；

**final：具有可见性**



## 内存屏障

我们都知道，为了性能优化，JMM在不改变正确语义的前提下，会允许编译器和处理器对指令序列进行重排序，那如果想阻止重排序要怎么办了？答案是可以添加内存屏障。

> **内存屏障**

JMM内存屏障分为四类见下图

![内存屏障分类表](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发关键字/内存屏障分类表.png)

Java编译器会在生成指令系列时在适当的位置会插入内存屏障指令来禁止特定类型的处理器重排序。为了实现volatile的内存语义，JMM会限制特定类型的编译器和处理器重排序，JMM会针对编译器制定volatile重排序规则表：

![volatile重排序规则表](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发关键字/volatile重排序规则表.png)

"NO"表示禁止重排序。为了实现volatile内存语义时，编译器在生成字节码时，会在指令序列中插入内存屏障来禁止特定类型的**处理器重排序**。对于编译器来说，发现一个最优布置来最小化插入屏障的总数几乎是不可能的，为此，JMM采取了保守策略：

1. 在每个volatile写操作的**前面**插入一个StoreStore屏障；
2. 在每个volatile写操作的**后面**插入一个StoreLoad屏障；
3. 在每个volatile读操作的**后面**插入一个LoadLoad屏障；
4. 在每个volatile读操作的**后面**插入一个LoadStore屏障。

需要注意的是：volatile写是在前面和后面**分别插入内存屏障**，而volatile读操作是在**后面插入两个内存屏障**

**StoreStore屏障**：禁止上面的普通写和下面的volatile写重排序；

**StoreLoad屏障**：防止上面的volatile写与下面可能有的volatile读/写重排序

**LoadLoad屏障**：禁止下面所有的普通读操作和上面的volatile读重排序

**LoadStore屏障**：禁止下面所有的普通写操作和上面的volatile读重排序

下面以两个示意图进行理解，图片摘自相当好的一本书《Java并发编程的艺术》。

![volatile写插入内存屏障示意图](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发关键字/volatile写插入内存屏障示意图.png)

![volatile读插入内存屏障示意图](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发关键字/volatile读插入内存屏障示意图.png)









## 先行发生原则

上面提到了可以用 volatile 和 synchronized 来保证有序性。除此之外，JVM 还规定了先行发生原则，让一个操作无需控制就能先于另一个操作完成。

### 1. 单一线程原则

> Single Thread rule

在一个线程内，在程序前面的操作先行发生于后面的操作。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/874b3ff7-7c5c-4e7a-b8ab-a82a3e038d20.png)

### 2. 管程锁定规则

> Monitor Lock Rule

一个 unlock 操作先行发生于后面对同一个锁的 lock 操作。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/8996a537-7c4a-4ec8-a3b7-7ef1798eae26.png)

### 3. volatile 变量规则

> Volatile Variable Rule

对一个 volatile 变量的写操作先行发生于后面对这个变量的读操作。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/942f33c9-8ad9-4987-836f-007de4c21de0.png)

### 4. 线程启动规则

> Thread Start Rule

Thread 对象的 start() 方法调用先行发生于此线程的每一个动作。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/6270c216-7ec0-4db7-94de-0003bce37cd2.png)

### 5. 线程加入规则

> Thread Join Rule

Thread 对象的结束先行发生于 join() 方法返回。

![](https://raw.githubusercontent.com/JourWon/image/master/Java并发编程-并发理论/233f8d89-31d7-413f-9c02-042f19c46ba1.png)

### 6. 线程中断规则

> Thread Interruption Rule

对线程 interrupt() 方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过 interrupted() 方法检测到是否有中断发生。

### 7. 对象终结规则

> Finalizer Rule

一个对象的初始化完成（构造函数执行结束）先行发生于它的 finalize() 方法的开始。

### 8. 传递性

> Transitivity

如果操作 A 先行发生于操作 B，操作 B 先行发生于操作 C，那么操作 A 先行发生于操作 C。