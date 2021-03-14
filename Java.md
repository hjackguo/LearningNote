### int范围

int占4B，32个bit.

-2^31< int < 2^31-1

int 的最大数 01111....1111 = 2^31-1, 第一位0表示正

int 的最小数 10000...0000 = -2^31, 第一位1表示负还有数字



### String, StringBuffer, StringBuilder区别

**String**

声明对象后不能修改，如果修改会重新开辟空间, 频繁操作的话，String会极大程度耗费时间。

**StringBuilder**

继承AbstractStringBuilder类

**线程不安全**

默认创建时,char[16]

```java
    // StringBuilder构建方法
		public StringBuilder() {
        super(16);
    }
		// AbstractStringBuilder构建方法
    AbstractStringBuilder(int capacity) {
        value = new char[capacity];
    }
```

Append方法，调用ensureCapacityInternal方法进行扩容,扩容为原来大小的2倍加2,  **n = 2*n + 2**。 调用copyOf拷贝到新的内存空间。

```java
    public AbstractStringBuilder append(String str) {
        if (str == null)
            return appendNull();
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    } 
	private void ensureCapacityInternal(int minimumCapacity) {
        // overflow-conscious code
        if (minimumCapacity - value.length > 0) {
          // 调用copyOf拷贝到新的内存空间
            value = Arrays.copyOf(value,
                    newCapacity(minimumCapacity));
        }
   }
    private int newCapacity(int minCapacity) {
        // overflow-conscious code
     		//  扩容后为原来大小的n*2+2
        int newCapacity = (value.length << 1) + 2;
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
    }
```

Arrays.copyOf的底层调用，调用**本地方法System.arraycopy**

```java
public static char[] copyOf(char[] original, int newLength) {
 	 char[] copy = new char[newLength];
  	System.arraycopy(original, 0, copy, 0,
                   Math.min(original.length, newLength));
  return copy;
}
```

**StringBuffer**

和StringBuilder基本一样，也继承了AbstractStringBuilder类

不过修改时加了**synchronized**，保证线程安全。但速度比StringBuilder慢。

```java
    @Override
    public synchronized StringBuffer append(CharSequence s) {
        toStringCache = null;
        super.append(s);
        return this;
    }
```

------

### Volatile关键字

**volatile的作用**

- 保证变量在线程之间的**可见性**
- **阻止**编译时和运行时的指令**重排序**。



**为什么会出现线程可见性问题**

要想解释为什么会出现线程可见性问题，需要从计算机处理器结构谈起。CPU要与内存进行交互，由于内存和CPU的速度有几个数量级的差距，为了提高CPU利用率，现代处理器结构都加入了一层读写速度尽可能接近CPU运算速度的 **高速缓存**来作为内存与CPU之间的缓存：将运算需要使用的数据复制到缓存中，让CPU运算可以快速进行，计算结束后再将**计算结果从缓存同步到主内存中**，这样处理器就无须等待缓慢的内存读写了。高速缓存的引入解决了CPU与内存之间速度上的矛盾，但是在多CPU系统中也带来了新的问题：**缓存一致性**。在多CPU系统中，每个CPU都有自己的高速缓存，所有的CPU又共享同一个主内存。如果多个CPU的运算任务都涉及到主内存中同一个变量时，那同步回主内存时以哪个CPU的缓存数据为准呢？这就需要各个CPU在数据读写的时候都遵循同一个协议进行操作。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go2qfhcdxpj313i0gi150.jpg" alt="WeChate8da95ccc2284408a218d94d7323592b.png" style="zoom:33%;" />

参考上图，假设有两个线程A、B分别在两个不同的CPU上运行，他们共享同一个变量X。如果线程A对X进行修改后，并没有将X更新后的结果同步到主内存，则变量X的修改对B线程是不可见的。所以**CPU与内存之间的高速缓存就是导致线程可见性问题的一个原因**。

CPU和主内存之间的高速缓存还会导致另一个问题——**重排序**。假设A、B两个线程共享两个变量X、Y，A和B分别在不同的CPU上运行。在A中先更改变量X的值，然后再更改变量Y的值。这时可能发生Y的值被同步回主内存，而X的值没有同步回主内存的情况，此时对于B来说是无法感知到X变量被修改的，或者可以认为对于B来说，Y变量的修改被重排序到X变量修改的前面。



Java内存模型称为**JMM**(Java Memory Model).

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go2l3yvm26j30uq0amk6z.jpg" alt="WeChatf6e82e920f48f4d2c7b42757005b4b93.png" style="zoom:33%;" />







**volatile特性之一：**

**保证变量在线程之间的可见性**。可见性的保证是基于CPU的内存屏障指令，被抽象为happens- before原则



可见性：当一个线程修改了变量的值，**新的值会立刻同步到主内存中**。而其它线程读取这个变量的时候，也会**从主内存中拉取最新的变量值**。

为什么volatile关键字可以有这样的特性？这得益于java语言的**先行发生原则**（happens-before）。



**volatile特性之二：**

阻止编译时和运行时的指令**重排序**。编译时JVM编译器遵循内存屏障约束，运行时依靠CPU屏障指令来阻止重排。



volatile**只能保证变量的可见性**，**不能保证变量的原子性**。

1. 当写一个volatile变量时，**JMM会把该线程本地内存中的变量强制刷新到主内存**中；
2. 这个写操作会**导致其他线程中的volatile变量缓存无效**。



什么时候适合用volatile呢？

1. 运行结果不依赖变量的当前值，或者**能够保证只有单一线程修改变量的值**。
2. 变量不需要与其他状态变量共同参与不变约束。

------

**volatile案例分析**

```java
public class VolatileDemo {
    /**
     执行结果
     start
     end
     */
    volatile boolean b = true;

    /**
     执行结果
     start
     */
    //boolean b = true;
    
    void m(){
        System.out.println("start");
        while (b){}
        System.out.println("end");
    }

    public static void main(String[] args) throws Exception e {
        VolatileDemo volatileDemo = new VolatileDemo();
        new Thread(()->{ volatileDemo.m(); }).start();
        TimeUnit.SECONDS.sleep(1);
        new Thread(()->{ volatileDemo.b = false; }).start();
    }
}
```

为什么加上volatile关键字后，其他线程就能读到已经改变的变量值呢？

**volatile修饰的变量是线程可见的**。当JVM解释volatile修饰的变量时，会通知CPU，**在计算过程中，每次使用变量参与计算时，都去检查内存中的数据是否发生变化**，而不是一直使用CPU缓存中的数据，可以保证数据最新。

但是，volatile**只能保证可见性**，**不能保证原子性**。 本例子中，可以通过在m函数前添加synchronized关键字。

------

### JAVA常见数据结构关系

**Collection**

- List
  - ArrayList
  - LinkedList
  - Vector
  - Stack (继承了Vector)
- Set
  - HashSet
  - TreeSet
  - LinkedHashSet

**Map**

- HashMap (键允许一个为null, 值允许多个null)
- HashTable (键和值不允许null,线程安全类)
- TreeMap
- LinkedHashMap
- ConcurrentHashMap



#### HashMap介绍

[美团HashMap介绍](https://tech.meituan.com/2016/06/24/java-hashmap.html)

> HashMap内部结构

HashMap = Node类型的数组+单向链表(LinkedList)/红黑树

数据类型 ： **Node<K,V>[] table**



官网文档：

Constructs an empty `HashMap` with the default **initial capacity (16)** and the **default load factor (0.75)**.

源码：

```java
    /**
     * The default initial capacity - MUST be a power of two.
     */
		// 扩容时 x<<1就行，2倍
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16

    /**
     * The load factor used when none specified in constructor.
     */
    static final float DEFAULT_LOAD_FACTOR = 0.75f;

		/**
		* Constructs an empty HashMap with the specified initial capacity and load factor.
		*/
		HashMap(int initialCapacity, float loadFactor)
      
    new HaspMap() <=> new HashMap(16,0.75)
```

> HashMap扩容

- 每put进一个Key-Value, size++,  threshold=Capacity*loadFacotor，当size==threshold时进行resize(扩容)
- 扩容后大小为原来的**2倍**
- 如果确定数据超过某个值，可以new HashMap(int initialCapacity)提前分配好，防止过程中扩容影响程序效率

> HashMap如何解决哈希冲突

链地址法

> 为什么哈希桶数组长度大小必须2^n?

一般来说，哈希桶大小设计为素数能减少碰撞。但HashMap采用2^n，主要为了在**取模和扩容时做优化**。

> 多次碰撞后链表过长如何解决？

红黑树。 当链表长度超过8，链表转为红黑树。

> 如何确定哈希桶位置？

```java
//计算key的哈希值
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
// 计算哈希值对应的idx
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```

本质上Hash算法就是三步：**取key的hashcode值(32位01)、高位运算、取模运算**。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go88dsqpgej31x81507wh.jpg" alt="WeChat35ecee009955feec6b29292005a12836.png" style="zoom:50%;" />



>  为什么不用key的hashcode直接对(哈希桶长度-1)取模就行,要这么麻烦？。

1. mod运算开销大
2. 当length长度比较小, hashcode只有低位值参与到&运算，高位怎么变，&结果都一样，冲突概率加大。

> 如何解决mod运算开销大？(为什么哈希桶长度要为2^n)

当**length长度为2的n次方**时，**h%length等价于h&(length-1)**巧妙实现了计算量问题。只保留length-1数字对应的二进制位。&比%具有更高的效率。

> 如何解决高位不参与index计算?

为了解决这个问题，hashmap加上hashcode的高16位异或低16位的"**扰动函数**",使得length长度比较小的情况下，高16位也能影响最终的哈希值，减少了冲突概率。

本图中，不扰动的话,HashCode前面28位不管怎么变，得到的index都是0

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go88m6744ej32640j0hdt.jpg" alt="WeChate9e84dcca9a589b33777f446e3f7d2ad.png" style="zoom:33%;" />

扰动后，index发生了变化

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go88onm5htj31yk0iohdt.jpg" alt="WeChatc5195c8537adaf6dbec17f5dbe5ce0e3.png" style="zoom: 33%;" />

------



#### 搜索树

##### 二叉搜索树(bst)

> 缺点

频繁增删后，会出现以下结构。 搜索复杂度变为O(n)

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go8ql2li2wj30r60ken2e.jpg" alt="WeChat4c8f08760065e0f522e257f57ecb6ea6.png" style="zoom: 33%;" />



##### 平衡二叉搜索树(avl)

主要思想： 追求极致的平衡，理想状态

Math.abs(左子树深度 - 右子树深度)<=1

> 缺点

每一次增删，由于最求绝对平衡，树要进行结构调整，耗时。

##### 红黑树

自平衡二叉查找树

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go8r0qmuyoj319w0m247x.jpg" alt="WeChat0400eb44b698975af4ed6a447f969608.png" style="zoom:33%;" />

> 红黑树特性

1. 每个结点要么是红的，要么是黑的。
2. 根节点是黑的。
3. 每个叶结点(树尾部的NULL结点)是黑的。
4. 如果一个结点是红的，那么它的两个子结点都是黑的。
5. 对于任一结点而言，其到叶结点树尾端的NULL指针的每一天路径都包含相同数目的黑结点。

这5条性质，使得n个结点的红黑树高度始终保持在h = log n。

> 红黑树比AVL树好在哪里

搜索上没AVL树好，但修改上，旋转次数比AVL树少。

> 红黑树三个操作

1. 红黑置换
2. 左旋
3. 右旋



### Java的反射机制

Reflection被视为动态语言的关键。 类加载完后，Java方法区就产生了一个Class类型的对象(一个类只有一个Class对象)，这个对象包含了类的结构信息。**这个对象就像一面镜子，透过这个镜子看到类的结构，所以称之为反射**。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go6kpssvljj31ck07utp9.jpg" alt="WeChatef7c2d9d0e615e094cbd2f09bc155074.png" style="zoom:50%;" />



```java
// 反射习惯类
import java.lang.reflect.Constructor;   // 类的构造器
import java.lang.reflect.Field;         // 属性
import java.lang.reflect.Method; 				// 方法
```



> 反射的提供的功能

- 运行时判断任意一个对象所属的类
- 在运行时构造任意一个类的对象
- 在运行时判断任意一个类所具有的成员变量和方法
- 在运行时获取范型信息
- 在运行时调用任意一个对象的成员变量和方法
- 在运行时处理注解
- 生成动态代理



> 疑问1：通过直接new的方式或反射的方式都可以调用公共的结构，开发中到底用哪个？

建议：直接new的方式。

什么时候会使用：反射的方式。 反射的特征：**动态性**。

> 疑问2：反射机制与面向对象中的封装性是不是矛盾的？如何看待两个技术？

 不矛盾。

#### 

> 关于java.lang.Class类的理解

1. 类的加载过程：

   .class文件被加载。加载到内存中的类，我们称为运行时类，此运行时类，就作为Class的一个实例。

2. Class的实例就对应着一个运行时类。

3. 运行时类存在方法区，可以通过不同方式获取此运行时类。



> 获取Class实例的方式

```java
/**
 获取Class实例的方式
 */
static void getClassObject() throws Exception{
    // 方式一: 调用运行时类的属性 : .class
    Class clazz1 = Person.class;
    System.out.println(clazz1); // class com.company.entity.Person
    // 方式二: 通过运行时类的对象, 调用getClass()
    Person p1 = new Person();
    Class clazz2 = p1.getClass();
    System.out.println(clazz2);
    // 方式三: 调用Class的静态方法：forName(String classPath)
    Class clazz3 = Class.forName("com.company.entity.Person");
    System.out.println(clazz3);
    // 方式四: 类的加载器： ClassLoader
    // 获取应用类加载器(又称系统类加载器)加载自己创建的类
    ClassLoader classLoader = ReflectionTest.class.getClassLoader();
    Class clazz4 = classLoader.loadClass("com.company.entity.Person");
    System.out.println(clazz4);

    // true 指向地址中同一个对象
    System.out.println(clazz1==clazz2); // true
    System.out.println(clazz2==clazz3); // true
    System.out.println(clazz1==clazz4); // true
}
```

1. 调运运行时类的属性 .class
2. 调用运行时类的对象，调用getClass()
3. 调用Class的静态方法： forName(String classPath)
4. 通过类的加载器加载 
   - 加载系统自带的类的话，如String, 用引导类加载器
   - 加载自定义类的话，用系统类加载器(又称应用类加载器)

