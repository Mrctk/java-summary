# ArrayList(JDK1.8)源码解析

既然是看源码，那我们要怎么看一个类的源码呢？这里我推荐的方法是：

1）看继承结构

​	看这个类的层次结构，处于一个什么位置，可以在自己心里有个大概的了解。

2）看构造方法

​	在构造方法中，看做了哪些事情，跟踪方法中里面的方法。

3）看常用的方法

​	跟构造方法一样，这个方法实现功能是如何实现的

**注**：既然是源码，为什么要这样设计类，有这样的继承关系。这就要说到设计模式的问题了。所以我们要了解常用的设计模式，才能更深刻的去理解这个类。

[toc]



## 简介
**ArrayList 是 Java 集合框架中 List 接口的一个实现类。底层是数组，相当于动态数组。与 Java 中的数组相比，它的容量能动态增长。**

`ArrayList`是`Vector`的翻版，区别在于`ArrayList`是线程不安全的，而`Vector`则是线程安全的。但是`Vector`是一个较老的集合，具有很多缺点，不建议使用，这里我们就不对其进行分析了。

ArrayList 可以说是我们使用最多的 List 集合，它有以下特点：

* 它是基于数组实现的List类
* 可以动态地调整容量
* 有序的（元素输出顺序与输入顺序一致）
* 元素可以为 null
* 不同步，非线程安全，效率高
* 查询快，增删慢
* 占用空间更小，对比 LinkedList，不用占用额外空间维护链表结构



ArrayList 为什么有这些优点呢？我们通过源码来分析分析。在阅读源码前先来看看ArrayList继承关系。

## 继承关系图

![ArrayList继承关系图](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL0phdmElRTklOUIlODYlRTUlOTAlODglRTYlQkElOTAlRTclQTAlODElRTglQUYlQTYlRTglQTclQTMlRTQlQjklOEJBcnJheUxpc3QvQXJyYXlMaXN0JUU3JUJCJUE3JUU2JTg5JUJGJUU1JTg1JUIzJUU3JUIzJUJCJUU1JTlCJUJFLnBuZw)

可以看到，`ArrayList`是`AbstractList`的子类，同时实现了`List`接口。除此之外，它还实现了三个标识型接口，这几个接口都没有任何方法，仅作为标识表示实现类具备某项功能。`RandomAccess`表示实现类支持快速随机访问，`Cloneable`表示实现类支持克隆，具体表现为重写了`clone`方法，`java.io.Serializable`则表示支持序列化，如果需要对此过程自定义，可以重写`writeObject`与`readObject`方法。



## 成员变量

```java
// 序列号
private static final long serialVersionUID = 8683452581122892189L;
// 数组初始容量为 10
private static final int DEFAULT_CAPACITY = 10;
// 空对象数组
private static final Object[] EMPTY_ELEMENTDATA = {};
// 缺省空对象数组
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};
// 底层数据结构，数组
transient Object[] elementData;
// 数组元素个数，默认为0
private int size;
// 最大数组容量
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
```



## 构造方法

```java
//默认构造方法，初始为空数组。
//只有插入一条数据后才会扩展为10，而实际上默认是空的
 public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

//根据指定容量创建对象数组
public ArrayList(int initialCapacity) {
    if (initialCapacity > 0) {
        //创建initialCapacity大小的数组
        this.elementData = new Object[initialCapacity];
    } else if (initialCapacity == 0) {
        //创建空数组
        this.elementData = EMPTY_ELEMENTDATA;
    } else {
        throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
    }
}

/**
 * 构造一个包含指定集合的元素的列表，按照它们由集合的迭代器返回的顺序。
 */
public ArrayList(Collection<? extends E> c) {
    //转换最主要的是toArray()，这在Collection中就定义了
    elementData = c.toArray();
    if ((size = elementData.length) != 0) {
        // c.toArray 有可能不返回一个 Object 数组
        if (elementData.getClass() != Object[].class)
            //使用 Arrays.copy 方法拷创建一个 Object 数组
            elementData = Arrays.copyOf(elementData, size, Object[].class);
    } else {
        // 替换为空数组
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

以无参数构造方法创建 ArrayList 时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量。即向数组中添加第一个元素时，数组容量扩为10。



## 内部类

```java
(1)private class Itr implements Iterator<E>  
(2)private class ListItr extends Itr implements ListIterator<E>  
(3)private class SubList extends AbstractList<E> implements RandomAccess  
(4)static final class ArrayListSpliterator<E> implements Spliterator<E>  
```

ArrayList有四个内部类，其中的**Itr是实现了Iterator接口**，同时重写了里面的**hasNext()**， **next()**， **remove()** 等方法；其中的**ListItr** 继承 **Itr**，实现了**ListIterator接口**，同时重写了**hasPrevious()**， **nextIndex()**， **previousIndex()**， **previous()**， **set(E e)**， **add(E e)** 等方法，所以这也可以看出了 **Iterator和ListIterator的区别**：ListIterator在Iterator的基础上增加了添加对象，修改对象，逆向遍历等方法，这些是Iterator不能实现的。



## 核心方法

### add()方法（有四个）

增和删是`ArrayList`最重要的部分，这部分代码需要我们细细研究

```java
//添加一个特定的元素到list的末尾
public boolean add(E e) {
    //先确保elementData数组的长度足够，size是数组中数据的个数，因为要添加一个元素，所以size+1，先判断size+1的这个个数数组能否放得下，在这个方法中去判断数组长度是否够用
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //在数据中正确的位置上放上元素e，并且size++
    elementData[size++] = e;
    return true;
}

//在指定位置添加一个元素
public void add(int index, E element) {
    rangeCheckForAdd(index);

    //先确保elementData数组的长度足够
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    //将数据整体向后移动一位，空出位置之后再插入，效率不太好
    System.arraycopy(elementData, index, elementData, index + 1,
                         size - index);
    elementData[index] = element;
    size++;
}

// 校验插入位置是否合理
private void rangeCheckForAdd(int index) {
    //插入的位置肯定不能大于size 和小于0
    if (index > size || index < 0)   
        //如果是，就报越界异常
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}

//添加一个集合
public boolean addAll(Collection<? extends E> c) {
    //把该集合转为对象数组
    Object[] a = c.toArray();
    int numNew = a.length;
    //增加容量
    ensureCapacityInternal(size + numNew);  // Increments modCount
    //挨个向后迁移
    System.arraycopy(a, 0, elementData, size, numNew);
    size += numNew;
    //新数组有元素，就返回 true
    return numNew != 0;
}

//在指定位置，添加一个集合
public boolean addAll(int index, Collection<? extends E> c) {
    rangeCheckForAdd(index);

    Object[] a = c.toArray();
    int numNew = a.length;
    ensureCapacityInternal(size + numNew);  // Increments modCount

    int numMoved = size - index;
    //原来的数组挨个向后迁移
    if (numMoved > 0)
        System.arraycopy(elementData, index, elementData, index + numNew,
                         numMoved);
    //把新的集合数组 添加到指定位置
    System.arraycopy(a, 0, elementData, index, numNew);
    size += numNew;
    return numNew != 0;
}
```

虽说 System.arraycopy 是底层方法，但每次添加都后移一位还是不太好。



### 对数组的容量进行调整

以上两种添加数据的方式都调用到了`ensureCapacityInternal`这个方法，我们看看它是如何完成工作的

```java
//确保内部容量够用
private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

//计算容量。判断初始化的elementData是不是空的数组,如果是空的话，返回默认容量10与minCapacity=size+1的较大值者。
private static int calculateCapacity(Object[] elementData, int minCapacity) {
    if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
        return Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    return minCapacity;
}

//确认实际的容量，这个方法就是真正的判断elementData是否够用
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    //minCapacity如果大于了实际elementData的长度，那么就说明elementData数组的长度不够用，不够用那么就要增加elementData的length。这里有的小伙伴就会模糊minCapacity到底是什么呢，这里解释一下

/**
     * 当我们要 add 进第1个元素到 ArrayList 时，elementData.length 为0 （因为还是一个空的 list），因为执行了 `ensureCapacityInternal()` 方法 ，所以 minCapacity 此时为10。此时，`minCapacity - elementData.length > 0 `成立，所以会进入 `grow(minCapacity)` 方法。
     * 当add第2个元素时，minCapacity 为2，此时e lementData.length(容量)在添加第一个元素后扩容成 10 了。此时，`minCapacity - elementData.length > 0 ` 不成立，所以不会进入 （执行）`grow(minCapacity)` 方法。
     * 添加第3、4···到第10个元素时，依然不会执行grow方法，数组容量都为10。
     * 直到添加第11个元素，minCapacity(为11)比elementData.length（为10）要大。进入grow方法进行扩容。
     */
    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        //ArrayList能自动扩展大小的关键方法就在这里了
        grow(minCapacity);
}

//扩容核心方法
private void grow(int minCapacity) {
    //将扩充前的elementData大小给oldCapacity
    // overflow-conscious code
    int oldCapacity = elementData.length;
    //新容量newCapacity是1.5倍的旧容量oldCapacity
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    //这句话就是适应于elementData就空数组的时候，length=0，那么oldCapacity=0，newCapacity=0，所以这个判断成立，在这里就是真正的初始化elementData的大小了，就是为10。
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    //如果newCapacity超过了最大的容量限制，就调用hugeCapacity，也就是将能给的最大值给newCapacity
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    //新的容量大小已经确定好了，就copy数组，改变容量大小。
    // minCapacity is usually close to size, so this is a win:
    elementData = Arrays.copyOf(elementData, newCapacity);
}

//这个就是上面用到的方法，很简单，就是用来赋最大值。
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    //如果minCapacity都大于MAX_ARRAY_SIZE，那么就Integer.MAX_VALUE返回，反之将MAX_ARRAY_SIZE返回。因为maxCapacity是三倍的minCapacity，可能扩充的太大了，就用minCapacity来判断了。
	//Integer.MAX_VALUE:2147483647   MAX_ARRAY_SIZE：2147483639  也就是说最大也就能给到第一个数值。还是超过了这个限制，就要溢出了。相当于arraylist给了两层防护。
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
    MAX_ARRAY_SIZE;
}
```

至此，我们彻底明白了`ArrayList`的扩容机制了。首先创建一个空数组**elementData**，第一次插入数据时直接扩充至10，然后如果**elementData**的长度不足，就扩充至1.5倍，如果扩充完还不够，就使用需要的长度作为**elementData**的长度。



### 大数据插入问题

这样的方式显然比我们例子中好一些，但是在遇到大量数据时还是会频繁的拷贝数据。那么如何缓解这种问题呢，`ArrayList`为我们提供了两种可行的方案：

- 使用`ArrayList(int initialCapacity)`这个有参构造，在创建时就声明一个较大的大小，这样解决了频繁拷贝问题，但是需要我们提前预知数据的数量级，也会一直占有较大的内存。
- 除了添加数据时可以自动扩容外，我们还可以在插入前先进行一次扩容。只要提前预知数据的数量级，就可以在需要时直接一次扩充到位，与`ArrayList(int initialCapacity)`相比的好处在于不必一直占有较大内存，同时数据拷贝的次数也大大减少了。这个方法就是**ensureCapacity(int minCapacity)**，其内部就是调用了`ensureCapacityInternal(int minCapacity)`。

我们这里使用ensureCapacity()方法测试

```java
public class EnsureCapacityTest {
	public static void main(String[] args) {
        ArrayList<Object> list = new ArrayList<Object>();
        final int N = 10000000;
        long startTime = System.currentTimeMillis();
        for (int i = 0; i < N; i++) {
            list.add(i);
        }
        long endTime = System.currentTimeMillis();
        System.out.println("使用ensureCapacity方法前：" + (endTime - startTime));

        list = new ArrayList<Object>();
        long startTime1 = System.currentTimeMillis();
        list.ensureCapacity(N);
        for (int i = 0; i < N; i++) {
            list.add(i);
        }
        long endTime1 = System.currentTimeMillis();
        System.out.println("使用ensureCapacity方法后：" + (endTime1 - startTime1));
    }
}
```

运行结果

```
使用ensureCapacity方法前：2700
使用ensureCapacity方法后：1054
```

通过运行结果，我们可以很明显的看出向 ArrayList 添加大量元素之前最好先使用`ensureCapacity` 方法，以减少增量重新分配的次数



### remove()方法

其实这几个删除方法都是类似的。

```java
//根据索引删除指定位置的元素
public E remove(int index) {
    //检查index的合理性
    rangeCheck(index);
	//这个作用很多，比如用来检测快速失败的一种标志。
    modCount++;
    //通过索引直接找到该元素
    E oldValue = elementData(index);

    //计算要移动的位数。
    int numMoved = size - index - 1;
    if (numMoved > 0)
        //移动元素，挨个往前移一位。
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    //将--size上的位置赋值为null，让gc(垃圾回收机制)更快的回收它。
    elementData[--size] = null; // clear to let GC do its work
	//返回删除的元素。
    return oldValue;
}

//从此列表中删除指定元素的第一个匹配项，如果存在，则删除。通过元素来删除该元素，就依次遍历，如果有这个元素，就将该元素的索引传给fastRemobe(index)，使用这个方法来删除该元素，fastRemove(index)方法的内部跟remove(index)的实现几乎一样，这里最主要是知道arrayList可以存储null值
public boolean remove(Object o) {
    if (o == null) {
        //挨个遍历找到目标
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                //快速删除
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}

//内部方法，“快速删除”，就是把重复的代码移到一个方法里
private void fastRemove(int index) {
    modCount++;
    int numMoved = size - index - 1;
    if (numMoved > 0)
        System.arraycopy(elementData, index+1, elementData, index,
                         numMoved);
    elementData[--size] = null; // clear to let GC do its work
}

//删除或者保留指定集合中的元素
//用于两个方法，一个removeAll()：它只清除指定集合中的元素，retainAll()用来测试两个集合是否有交集。　
private boolean batchRemove(Collection<?> c, boolean complement) {
    //将原集合，记名为A
    final Object[] elementData = this.elementData;
    //r用来控制循环，w是记录有多少个交集
    int r = 0, w = 0;
    boolean modified = false;
    try {
        //遍历 ArrayList 集合
        for (; r < size; r++)
            //参数中的集合c一次检测集合A中的元素是否有
            if (c.contains(elementData[r]) == complement)
                //有的话，就给集合A
                elementData[w++] = elementData[r];
    } finally {
        //发生了异常，直接把 r 后面的复制到 w 后面
        if (r != size) {
            //将剩下的元素都赋值给集合A
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            w += size - r;
        }
        if (w != size) {
            //这里有两个用途，在removeAll()时，w一直为0，就直接跟clear一样，全是为null。
            //retainAll()：没有一个交集返回true，有交集但不全交也返回true，而两个集合相等的时候，返回false，所以不能根据返回值来确认两个集合是否有交集，而是通过原集合的大小是否发生改变来判断，如果原集合中还有元素，则代表有交集，而元集合没有元素了，说明两个集合没有交集。
            // 清除多余的元素，clear to let GC do its work
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}


//保留公共的
public boolean retainAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, true);
}

//将elementData中每个元素都赋值为null，等待垃圾回收将这个给回收掉
public void clear() {
    modCount++;
    //并没有直接使数组指向 null,而是逐个把元素置为空，下次使用时就不用重新 new 了
    for (int i = 0; i < size; i++)
        elementData[i] = null;

    size = 0;
}
```


　　总结：根据索引删除指定位置的元素，此时会把指定下标到数组末尾的元素挨个向前移动一个单位，并且会把数组最后一个元素设置为null，这样是为了方便之后将整个数组不被使用时，会被GC，可以作为小的技巧使用。



### get()方法

```java
public E get(int index) {
    // 检验索引是否合法
    rangeCheck(index);

    return elementData(index);
}

private void rangeCheck(int index) {
    if (index >= size)
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
}
```

说明：get函数会检查索引值是否合法（只检查是否大于size，而没有检查是否小于0），值得注意的是，在get函数中存在element函数，element函数用于返回具体的元素，具体函数如下：

```java
E elementData(int index) {
    return (E) elementData[index];
}
```

说明：返回的值都经过了向下转型（Object -> E），这些是对我们应用程序屏蔽的小细节。



### set()方法

```java
//设定指定下标索引的元素值
public E set(int index, E element) {
    // 检验索引是否合法
    rangeCheck(index);
    // 旧值
    E oldValue = elementData(index);
    // 赋新值
    elementData[index] = element;
    // 返回旧值
    return oldValue;
}
```



### indexOf()方法

```java
// 从首开始查找数组里面是否存在指定元素
public int indexOf(Object o) {
    // 查找的元素为空
    if (o == null) { 
        // 遍历数组，找到第一个为空的元素，返回下标
        for (int i = 0; i < size; i++) 
            if (elementData[i]==null)
                return i;
    } else { 
        // 查找的元素不为空
        // 遍历数组，找到第一个和指定元素相等的元素，返回下标
        for (int i = 0; i < size; i++) 
            if (o.equals(elementData[i]))
                return i;
    } 
    // 没有找到，返回空
    return -1;
}

//返回列表中指定元素最后一次出现的索引，倒着遍历
public int lastIndexOf(Object o) {
    if (o == null) {
        for (int i = size-1; i >= 0; i--)
            if (elementData[i]==null)
                return i;
    } else {
        for (int i = size-1; i >= 0; i--)
            if (o.equals(elementData[i]))
                return i;
    }
    return -1;
}
```

说明：从头开始查找与指定元素相等的元素，需要注意的是可以查找null元素，意味着ArrayList中可以存放null元素的。与此函数对应的lastIndexOf，表示从尾部开始查找。



### contains()方法

```java
//判断是否含有某个元素
public boolean contains(Object o) {
    return indexOf(o) >= 0;
}
```



### toArray()方法

```java
/**
     以正确的顺序返回一个包含此列表中所有元素的数组（从第一个到最后一个元素）; 返回的数组的运行时类型是指定数组的运行时类型。 
     */
public Object[] toArray() {
    //elementData：要复制的数组；size：要复制的长度
    return Arrays.copyOf(elementData, size);
}

public <T> T[] toArray(T[] a) {
    //如果只是要把一部分转换成数组
    if (a.length < size)
        // Make a new array of a's runtime type, but my contents:
        return (T[]) Arrays.copyOf(elementData, size, a.getClass());
    //全部元素拷贝到 数组 a
    System.arraycopy(elementData, 0, a, 0, size);
    if (a.length > size)
        a[size] = null;
    return a;
}
```





## System.arraycopy()和 Arrays.copyOf()方法

阅读源码的话，我们就会发现 ArrayList 中大量调用了这两个方法。比如：我们上面讲的扩容操作以及`add(int index, E element)`、`E remove(int index)`、`toArray()` 等方法中都用到了该方法！

### System.arraycopy()方法

System.arraycopy(...)：将指定源数组中的数组从指定位置开始复制到目标数组的指定位置。

```java
// src：源对象
// srcPos：源对象对象的起始位置
// dest：目标对象
// destPost：目标对象的起始位置
// length：从起始位置往后复制的长度。
// 这段的大概意思就是解释这个方法的用法，复制src到dest，复制的位置是从src的srcPost开始，到srcPost+length-1的位置结束，复制到destPost上，从destPost开始到destPost+length-1的位置上
public static void arraycopy(Object src, int srcPos, Object dest, int destPos,
             int length)
```

简单的方法测试以下：

```java
public class ArraycopyTest {

	public static void main(String[] args) {
        int[] a = new int[10];
        a[0] = 0;
        a[1] = 1;
        a[2] = 2;
        a[3] = 3;
        System.arraycopy(a, 2, a, 3, 3);
        a[2] = 99;
        for (int i = 0; i < a.length; i++) {
            System.out.print(a[i] + " ");
        }
    }

}
```

结果：

```
0 1 99 2 3 0 0 0 0 0 
```



### Arrays.copyOf()方法

Array.copyOf() 选择指定的数组，截断或填充空值（如果需要），使副本具有指定的长度。以达到扩容的目的

```java
//Arrays的copyOf()方法传回的数组是新的数组对象，改变传回数组中的元素值，不会影响原来的数组。
//copyOf()的第二个自变量指定要建立的新数组长度，如果新数组的长度超过原数组的长度，则保留数组默认值
public static <T> T[] copyOf(T[] original, int newLength) {
    return (T[]) copyOf(original, newLength, original.getClass());
}

/**
 * @Description 复制指定的数组, 如有必要用 null 截取或填充，以使副本具有指定的长度
 * 对于所有在原数组和副本中都有效的索引，这两个数组相同索引处将包含相同的值
 * 对于在副本中有效而在原数组无效的所有索引，副本将填充 null，当且仅当指定长度大于原数组的长度时，这些索引存在
 * 返回的数组属于 newType 类
 *
 * @param original 要复制的数组
 * @param newLength 副本的长度
 * @param newType 副本的类
 * 
 * @return T 原数组的副本，截取或用 null 填充以获得指定的长度
 * @throws NegativeArraySizeException 如果 newLength 为负
 * @throws NullPointerException 如果 original 为 null
 * @throws ArrayStoreException 如果从 original 中复制的元素不属于存储在 newType 类数组中的运行时类型
 */

public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
    @SuppressWarnings("unchecked")
    T[] copy = ((Object)newType == (Object)Object[].class)
        ? (T[]) new Object[newLength]
        : (T[]) Array.newInstance(newType.getComponentType(), newLength);
    System.arraycopy(original, 0, copy, 0,
                     Math.min(original.length, newLength));
    return copy;
}
```

测试代码如下：

```java
public static void main(String[] args) {
        int[] a = new int[3];
        a[0] = 0;
        a[1] = 1;
        a[2] = 2;
        int[] b = Arrays.copyOf(a, 10);
        System.out.println("b.length " + b.length);

        for (int i : b) {
            System.out.print(i + " ");
        }
    }
```

结果：

```
b.length10
0 1 2 0 0 0 0 0 0 0 
```



### 两者联系与区别

**联系：**
看两者源代码可以发现`copyOf()`内部调用了`System.arraycopy()`方法
**区别：**

1. arraycopy()需要目标数组，将原数组拷贝到你自己定义的数组里，而且可以选择拷贝的起点和长度以及放入新数组中的位置
2. copyOf()是系统自动在内部新建一个数组，并返回该数组。



