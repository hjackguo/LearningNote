

![f3c480e0-3be2-11e9-82f8-0f2e500ca934](/Users/guohuanjie/Documents/programming/LearnNote/image/f3c480e0-3be2-11e9-82f8-0f2e500ca934.jpg)

### Java基础

#### 1.JDK和JRE有什么区别？

- JDK：Java Development Kit 的简称，Java开发工具包，提供了Java的开发环境和运行环境。
- JRE：Java Runtime Environment的简称，Java运行环境，为Java提供了所需环境。

区别（Jre是Jdk中的一部分，JDK出了运行环境，还提供开发环境）

#### 2.==和Equal区别是什么？

**==解读**

对于基本类型和引用类型==的作用效果是不同的，如下：

- 基本类型：比较**值**是否相同；
- 引用类型：比较的是**引用**是否相同；

代码示例：

```java
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
```

代码解读： x和y指向同一个引用，所以==是true，而new String()方法重写开辟了内存空间，所以==结果为false，而equals一直是值的比较，所以结果都是true。

**equals解读**

equals本质上就是==，只不过String和Integer等重写了equals方法，把它变成了值的比较。

首先来看默认情况下equals比较一个相同值的对象。

```java
classCat{
    publicCat(String name){
        this.name = name;
    }

    private String name;

    public String getName(){
        return name;
    }

    publicvoidsetName(String name){
        this.name = name;
    }
}

Cat c1 = new Cat("王磊");
Cat c2 = new Cat("王磊");
System.out.println(c1.equals(c2)); // false
```

输出结果出乎意料，为什么是false？

equals源码：

```java
publicbooleanequals(Object obj){
        return (this == obj);
}
```

原来equals本质就是==。

两个相同值的string对象，为什么返回就是true？

```java
String s1 = new String("老王");
String s2 = new String("老王");
System.out.println(s1.equals(s2)); // true
```

进入String类的equals方法，找到了答案：

```java
publicbooleanequals(Object anObject){
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

string的equals方法把饮用比较改成了值比较，对每个char进行了对比。

**总结：** ==对于基本类型来说是值比较（常量池只为"abc"分配一次空间）, 对于引用类型来说是比较引用的是否为常量池中的同一个地址； 而equals默认情况下是引用比较，只是很多类**重写**了equals方法，比如String、Integer等把它变成值引用，所以一般情况下equals比较的是值是否相等。

#### 3.两个对象的hashCode()相同，equals()不一定true

```Java
String str1 = "通话";
String str2 = "重地";
System. out. println(String. format("str1：%d | str2：%d",  str1. hashCode(),str2. hashCode()));
System. out. println(str1. equals(str2));
```

执行的结果：

```java
str1：1179395 | str2：1179395

false
```

解读：hash可能出现碰撞，概率小！

#### 4.final在Java中有什么作用？

- final修饰的类叫最终类，不能够被继承
- final修饰的方法不能被重写
- final修饰的变量叫常量，常量必须初始化，并初始化后值不能被修改

#### 5.Java中的Math.round(-1.5)等于多少？

等于-1，因为在数轴上取值时，中间值（0.5）向右取整，所以正0.5向上取整，负0.5是直接舍弃。

#### 6.String属于基础的数据类型吗？

String不属于基础类型，基础类型有8种： byte、boolean、char、short、in t、float、long、double，而String属于**对象**。

#### 7.Java中操作字符串有哪些类？它们之间有什么区别？

操作字符串的类有：**String、StringBuffer、StringBuilder**。

String声明的是不可变的对象，每次操作都会生成新的String对象，然后将指针指向新的String对象，而StringBuffer、StringBuilder可以在原有对象的基础上进行操作，所以在经常**改变字符串内容的情况下最好不要使用String**。

#### 8.String str="i"与String str = new String("i")一样吗？

不一样，因为内存的分配方式不一样。String str="i"的方式，Java虚拟机会将其分配到**常量池**中；而String str = new String("i")会被分配到**堆内存**中。

#### 9.如何将字符串反转？

使用StringBuilder或者StringBuffer的reverse()方法；

示例代码：

```Java
// StringBuffer reverse
StringBuffer stringBuffer = new StringBuffer();
stringBuffer. append("abcdefg");
System. out. println(stringBuffer. reverse()); // gfedcba
// StringBuilder reverse
StringBuilder stringBuilder = new StringBuilder();
stringBuilder. append("abcdefg");
System. out. println(stringBuilder. reverse()); // gfedcba
```

#### 10.String类常用的方法有哪些？

- indexOf():返回指定字符的索引。
- charAt():返回指定索引的字符。
- replace():字符串替换。
- trim():去除字符串两端空白。
- split():分割字符串，返回一个分割后的数组。
- getBytes()：返回字符串的byte类型数组。
- length():字符串长度
- toLowerCase()
- toUpperCase()
- substring
- equals

#### 11.抽象类必须要有抽象方法吗？

不需要，抽象类不一定非要有抽象方法。

```java
abstract classCat{
    publicstaticvoidsayHi(){
        System. out. println("hi~");
    }
}
```

上面代码，抽象类并没有抽象方法但完全可以正常运行。

#### 12.普通类和抽象类有哪些区别？

- 普通类不能包含抽象方法，抽象类可以包含抽象方法。
- 抽象类不能直接实例化，普通类可以直接实例化。





tips: 类的四大特性：封装、继承、抽象、多态

#### 13.抽象类能使用final修饰吗？

不能，定义抽象类就是让其他类集成的，如果定义为**final类就不能被继承**，这样彼此就会产生矛盾。

![Screenshot 2020-11-19 at 23.25.42](/Users/guohuanjie/Documents/programming/LearnNote/image/Screenshot 2020-11-19 at 23.25.42.png)

```java
public abstract class Employee {
    protected String name;
    protected boolean gender;
    protected int age;
    public Employee(String name,boolean gender){
        this.name=name;
        this.gender=gender;
    }
    /**
    * 继承本抽象类的子类必须实现getsalary方法
    */
    public abstract void getsalary();
    public void whoami(){
        System.out.println("我是"+name);
    }
}
```

#### 14.接口和抽象类有什么区别？

- 实现：抽象类的子类用extends来继承；接口必须使用implement来实现接口。
- 构造函数：抽象类可以有构造函数；接口不能有；
- 实现数量：类可以实现多个接口；但是只能继承一个抽象类；
- 访问修饰符：接口中的方法默认使用public修饰；抽象类中的方法可以是任意访问修饰符。

#### 15.Java中IO流分为几种？

按功能来分：输入流(input)、输出流(output)

按类型来分：字节流和字符流。

字节流和字符流区别：字节流按8位传输以字节为单位输入输出数据，字符流按16位传输以字符为单位输入输出数据。

#### 16.BIO、NIO、AIO有什么区别？

- BIO：Block IO**同步阻塞式**IO，就是我们平时使用的传统IO，它的特点是模式**简单**使用方便，**并发处理能力低**。
- NIO： Non IO**同步非阻塞**IO，是传统IO的升级，**客户端和服务器**通过Channel(通道)通讯，实现了**多路复用**。
- AIO：Asynchronous IO是**NIO的升级**，也叫**NIO2**，实现了**异步非阻塞IO**，异步IO的操作基于**事件**和**回调机制**。

#### 17.Files的常用方法都有哪些？

- exists():检测文件路径是否存在。
- createFile()
- createDirectory()：创建文件夹
- delete():删除一个文件或目录
- copy()
- move()
- size()：查看文件个数
- read()
- write()

### 容器

#### 18.Java容器都有哪些？

Java容器分为Collection和Map两大类，其下面又有很多子类。

- Collection
  - List
    - ArrayList
    - LinkedList
    - Vector
    - Stack
  - Set
    - HashSet
    - LinkedHashSet
    - TreeSet

- Map
  - HashMap
    - LinkedHashMap
  - TreeMap
  - ConcurrentHashMap
  - Hashtable

#### 19.Collection和Collections有什么区别?

- Collection是一个集合接口，它提供了对集合对象进行基本操作的通用接口方法，所有集合都是它的子类，比如List、Set等。
- Collections是一个包装类，包含了很多静态方法，不能被实例化，就像一个工具类，比如提供的排序方法：Collections.sort(list)。

#### 20.List、Set、Map之间的区别是什么？

List、Set、Map的区别主要体现在两个方面：元素**是否有序**、是否允许元素**重复**。

![Screenshot 2020-11-20 at 00.26.42](/Users/guohuanjie/Documents/programming/LearnNote/image/Screenshot 2020-11-20 at 00.26.42.png)

#### 21.HashMap和HashTable有什么区别

- 存储：HashMap允许key和value为null，而Hashtable不允许

```java
        // HashMap
				Map map = new HashMap<>();
        map.put(null,1);
        System.out.println(map);  // {null=1}
        System.out.println(map.get(null));  //1
```

```java
        // Hashtable
				Map map = new Hashtable();
        map.put(1,null);
// Exception in thread "main" java.lang.NullPointerException
	at java.base/java.util.Hashtable.put(Hashtable.java:476)

    
        Map map = new Hashtable();
        map.put(null,1);
//Exception in thread "main" java.lang.NullPointerException: Cannot invoke "Object.hashCode()" because "key" is null
	at java.base/java.util.Hashtable.put(Hashtable.java:481)
```

- 线程安全： Hashtable是**线程安全**的，而HashMap是**非线程安全**的。
- 推荐使用：Hashtable是保留类**不建议使用**，推荐在**单线程**环境下使用**HashMap**替代。,**多线程**使用**ConcurrentHashMap**替代。

#### 22.如何决定使用HashMap还是TreeMap?

对于在Map中**插入、删除、定位**一个元素这类操作，**HashMap**是最好的选择，因为相对而言HashMap插入会更快，但如果你要对一个**Key集合进行有序遍历**，那**TreeMap**是更好的选择。

#### 23.说一下HashMap的实现原理？

HashMap = Node**数组** + **链表或红黑树**

HashMap基于**Hash算法**实现的，我们通过**put**(key,value)存储，**get**(key)来获取。当传入key时，HashMap会根据**key.hashCode()**计算出hash值，根据Hash值将value保存在bucket里。当计算出的**hash相同**时，我们称之为**hash冲突**，HashMap的做法是用链表和红黑树存储相同hash值的value。当hash冲突**个数少**时，使用**链表**，**个数超过8**使用**红黑树**。

<img src="/Users/guohuanjie/Documents/programming/LearnNote/image/8db4a3bdfb238da1a1c4431d2b6e075c_1440w.png" alt="8db4a3bdfb238da1a1c4431d2b6e075c_1440w" style="zoom:50%;" />

------

**Node**

```java
static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;    //用来定位数组索引位置
        final K key;
        V value;
        Node<K,V> next;   //链表的下一个node

        Node(int hash, K key, V value, Node<K,V> next) { ... }
        public final K getKey(){ ... }
        public final V getValue() { ... }
        public final String toString() { ... }
        public final int hashCode() { ... }
        public final V setValue(V newValue) { ... }
        public final boolean equals(Object o) { ... }
}
```

Node是HashMap的一个内部类，实现**Map.Entry**接口，本质就是一个**映射(键值对)**。

------

**Q.HashMap扩容机制**

```java
     int threshold;             // 所能容纳的key-value对极限 
     final float loadFactor;    // 负载因子
     int modCount;  						// 结构变化数
     int size;                  // 实际键值对数量
```

Node[] table的初始化长度**length**(默认值**16**)，**LoadFactor**为负载因子(默认值**0.75**), **threshold**是HashMap容纳最大数据量的Node个数。

**threshold = length*LoadFactor**

如果存储超过threshold，需要重新**resize**(扩容)，扩容后HashMap容量是之前容量**两倍**。

哈希桶数组table长度大小必须是**2的n次方**。目的：**取模**和**扩容**时优化。

------



#### 24.说一下HashSet的实现原理？

HashSet是基于HashMap实现的，HashSet**底层使用HashMap**来保存所有元素，因此HashSet的实现比较简单，相关HashSet的操作，基本上都是直接调用底层HashMap相关方法来完成，HashSet**不允许重复的值**。

#### 25.ArrayList和LinkedList的区别是什么？

- 数据结构实现：ArrayList是**动态数组**的数据结构实现，实例化一个ArrayList的时候，默认开辟size=**10**，**扩容**时新建**1.5**倍空间，复制过去。而LinkedList是**双向链表**的数据结构实现。
- 随机访问效率：**ArrayList**比LinkedList在**随机访问**的时候**效率更高**，因为LinkedList是线性的数据存储方式，所以需要移动指针从前往后依次查找。而ArrayList直接通过下标访问。
- 增加和删除效率：在**非首位**的增加和删除操作，LinkedList要比ArrayList效率高，因为**ArrayList增删操作要影响数组内其它数据的下标**。

Q.哪个更占用内存？

一般情况下，LinkedList更占用内存。

但如果大数据量情况下，ArrayList进行1.5倍扩容后，扩容空间只放了少量数据，ArrayList可能更加占用内存。

#### 26.如何实现数组和List之间的转换？

- 数组转List：使用Arrays.asList(array)转换
- List转数组：使用List自带的toArray()方法。

代码示例：

```Java
// list to array
List<String> list = new ArrayList<String>();
list. add("王磊");
list. add("的博客");
list. toArray();
// array to list
String[] array = new String[]{"王磊","的博客"};
Arrays. asList(array);
```

#### 27.ArrayList和Vector的区别是什么？

- 线程安全：**Vector**使用了**Synchronized**来实现**线程同步**，是**线程安全**的，而**ArrayList**是**非线程安全**的。

- 性能：ArrayList在性能方面优于Vector。

- 扩容：ArrayList和Vector都会根据实际的需要动态调整容量，只不过在**Vector**扩容每次会增加**1倍**，而**ArrayList**只会增加**50%**。

  ```java
  Vector<Integer> vec = new Vector<>();//Synchronized-线程同步
  vec.add(1);
  vec.add(2);
  System.out.println(vec); //[1, 2]
  ```

#### 28.Array和ArrayList有何区别？

- **Array**可以存储**基本数据类型和对象**，ArrayList只能存储**对象**。

  ```java
          // array存储基本数据类型
          int [] a = new int[10];
          // array存储对象
          Integer[] b = new Integer[10];
          // ArrayList存储对象
          ArrayList<Integer> c = new ArrayList<>();
  ```

  

- Array是指定固定大小的，而ArrayList大小是自动扩展的。

- Array内置方法没有ArrayList多，比如addAll、removeAll、iteration等方法只有ArrayList有。

#### 29.在Queue中poll()和remove()有什么区别？

- 相同点：都是**返回第一个元素**，并在队列中**删除返回的对象**。
- 不同点：如果没有元素**poll()**会**返回null**，而**remove()**会直接**抛出NoSuchElementException异常**。

#### 30.哪些集合类是线程安全的？

**Vector、HashTable、Stack都是线程安全**的，而像HashMap则是非线程安全的，不过在JDK1.5之后随着Java.util.concurrent并发包的出现，它们也有了自己对于的线程安全类，比如**HashMap对应的线程安全类**是**ConcurrentHashMap**。

#### 31.迭代器Iterator是什么？

Iterator接口提供遍历任何Collection的接口。我们可以从任何一个Collection中使用iteration来获取迭代器实例。

#### 32. Iterator怎么用？有什么特点？

```java
        Vector<Integer> vec = new Vector<>();
        vec.add(1);
        vec.add(2);
        Iterator<Integer> ite = vec.iterator();
        while (ite.hasNext()){
            System.out.println(ite.next()); // 1,2
        }
```

Iterator的特点是更加安全，因为它可以确保，在**当前遍历集合元素被修改**时，抛出**ConcurrentModificationException**异常。

```java
        Vector<Integer> vec = new Vector<>();
        vec.add(1);
        vec.add(2);
        Iterator<Integer> ite = vec.iterator();
				// listIterator.previous() 方法可以使其双向访问
        ListIterator<Integer> ite2 = vec.listIterator();
        while (ite.hasNext()){
            vec.add(3);
            System.out.println(ite.next());
        }
// Exception in thread "main" java.util.ConcurrentModificationException
```

#### 33.Iterator和ListIterator有什么区别？

- **Iterator**可以遍历**Set和List**，而**ListIterator**只能遍历**List**。
- Iterator只能**单向遍历**，而ListIterator能**双向遍历**。
- ListIterator从Iterator**继承**，然后添加了一些额外的功能，比如添加一个元素、替换、获取前后元素索引。

#### 34.怎么确保一个集合不能被修改？

可以使用Collections.**unmodifiableCollection**(Collection c)方法来创建一个只读集合，这样改变集合的任何操作都会抛出Java.lang.UnsupportedOperationException异常。

```java
        Vector<Integer> vec = new Vector<>();
        vec.add(1);
        Collection<Integer> unchange = Collections.unmodifiableCollection(vec);
        unchange.add(2);         // 报错 Exception in thread "main" java.lang.UnsupportedOperationException
```

### 多线程

#### 35.并发和并行有什么区别

- 并行：**多个处理器**或多核处理器同时处理多个任务。
- 并发：多个任务在**同一个CPU核**上，按**时间片**轮流执行，从逻辑上来看那行任务时同时执行。

#### 36.线程和进程的区别？

一个程序下至少有一个进程，一个进程下至少有一个线程，一个进程下也可以有多个线程来增加程序的执行速度。

#### 37.守护线程是什么？

守护线程是运行在后台的一种特殊线程。它独立于控制终端并且**周期性地**执行某种任务或等待处理某些发生的时间。在Java中**垃圾回收线程**就是特殊的守护线程。

#### 38.创建线程有哪几种方式？

创建线程有三种方式：

- **继承**Thread重写run方法；
- 实现**Runnable**接口；
- 实现**Callable**接口。

#### 39.说一下runnable和callable有什么区别？

**runnable没有返回值**，**callable可以拿到返回值**，callable可以看作是runnable的补充。

```java
        CallableExample ex = new CallableExample();
        RunnableExample ru = new RunnableExample();
        ru.run(); // void
        Object object = ex.call(); // 返回object
```

#### 40.线程的状态有哪些？

- NEW  尚未启动
- RUNNABLE  正在执行中
- BLOCKED  阻塞的(被同步锁或者IO锁阻塞)
- WAITING 永久等待状态
- TIMED_WAITING 等待指定的时间重新被唤醒的状态
- TERMINATED 执行完成



#### 41.sleep()和wait()有什么区别？

- 类的不同：sleep()来自**Thread**，wait()来自**Object**。
- 释放锁：sleep(**)不释放锁**；wait()**释放锁**。
- sleep**不依赖同步**方法，**wait**需要。
- 用法不同：sleep()**到时间会自动恢复**；wait()可以使用**notify()/notifyAll()唤醒**,在同步时使用。

```java
public class WaitSleep {
    private final static Object lock = new Object();
    public static void main(String[] args) {
        Stream.of("线程1","线程2").forEach(n->new Thread(n){
            @Override
            public void run() {
                WaitSleep.testWait();
//                WaitSleep.testSleep();

            }
        }.start());

    }
    /**
     * 测试sleep 不释放锁
     线程1正在运行
     线程1休眠结束
     线程2正在运行
     线程2休眠结束
     */
    private static void testSleep(){
        //synchronized ((lock)){
            try{
             System.out.println(Thread.currentThread().getName()+"正在运行");
                Thread.sleep(1000);
              System.out.println(Thread.currentThread().getName()+"休眠结束");
            }catch (Exception e){
                e.printStackTrace();
            }
        //}
    }

    /**
     * wait释放锁
     线程1正在运行
     线程2正在运行
     线程2休眠结束
     线程1休眠结束
     */
    private static void testWait(){
      	// 如果去除synchronized 报错java.lang.IllegalMonitorStateException: current thread is not owner
        synchronized ((lock)){
            try{
                System.out.println(Thread.currentThread().getName()+"正在运行");
                lock.wait(1000);
                System.out.println(Thread.currentThread().getName()+"休眠结束");
            }catch (Exception e){
                e.printStackTrace();
            }
        }
    }
}
```

#### 42. notify()和notifyAll()有什么区别？

notifyAll()会唤醒**所有**的线程，notify()只会唤醒**一个**线程。notifyAll调用后，会将全部线程由**等待池转移到锁池**，然后参与**锁的竞争**，竞争成功则继续执行，如果不成功则留在锁池等待锁被释放后再次参与竞争。而notify()只会唤醒一个线程，具体唤醒哪一个由**虚拟机控制**。

#### 43.线程的run()和start()有什么区别？

start()方法用于**启动线程**，run()方法用于执行线程的**运行时代码**。**run()**可以**重复调用**，而**start()只能调用一次**。

#### 44.创建线程池有哪几种方式？

------

线程池的创建有七种方式，最核心的是最后一种：

- **newSingleThreadExecutor**(): 它的特点在于工作线程数亩被限制为**1**，操作一个**无界的工作队列，所以它保证了所有任务都是被**顺序执行**，最多会有一个任务处于活动状态，并且不允许使用者改动线程池实例，因此可以避免其改变线程数目。
- **newCachedThreadPool**(): 它是一种用来处理大量短时间工作任务的线程池，特点：它会**试图缓存线程并重用**，当**无缓存线程可用时，就会创建新的工作线程**；如果线程限制时间超过60秒，则被制止并移出缓存；长时间闲置时，这种线程池不会消耗什么资源。内部使用**synchronousQueue**作为工作队列。
- **newFixedThreadPool**(int nThreads): **重用指定数量的线程**，其背后使用的是**无界**的工作队列，任何时候**最多有n个工作线程是活动**的。这意味着，如果任务数量超过了活动队列数目，将在工作队列中**等待**空闲线程出现。
- **newSingleThreadScheduledExecutor**(): 创建**单线程池**，返回**ScheduledExecutorService**,可以进行**定时或周期性**的工作调度。
- **newScheduledThreadPool**(int corePoolSize): 和newSingleThreadScheduledExecutor()类似，创建的是个ScheduledExecutorService,可以进行**定时**或**定期任务**的调度，区别在于单一工作线程还是**多个工作线程**。
- **newWorkStealingPool**(int parallelism)：这是一个经常被人忽略的线程池，其内部会**构建ForkJoinPool**，利用**work-Stealing算法**，**并行地处理任务**，**不保证处理顺序**。
- **ThreadPoolExecutor**():是**最原始**的线程池创建，上面1-3种都是对ThreadPoolExecutor的封装。(**核心)**

------

**线程池作用**

- **帮我们管理线程，避免增加创建线程和销毁线程的资源损耗。**线程其实是一个对象，创建一个对象，需要结果类的加载，销毁一个对象，需要走GC垃圾回收，都是需要资源开销的。
- **提高响应速度。**如果任务到达了，相当于从线程池里拿线程，比重新创建一条线程快。
- **重复利用。**线程用完，再放回池子，可达到重复利用效果，节省资源。

------

**线程池创建**

```java
public ThreadPoolExecutor(
  	//线程池的基本大小，即在没有任务需要执行的时候线程池的大小
 	  int corePoolSize,   
  	// 1.线程池最大线程数大小。 同时创建的总线程>maximumPoolSize+Queue，触发饱和策略。 先使用队列，再使用maximumPoolSize.
    // 2.如果使用了无界队列，则创建无限线程，直到内存溢出，maximumPoolSize永远用不上
  	int maximumPoolSize,
  	// 线程池中非核心线程空闲的存活时间大小
  	long keepAliveTime,
  	// 单位
  	TimeUnit unit,
  	// 存放任务的阻塞队列  BlockQueue 阻塞队列，线程安全的。 Queue是线程不安全的
  	BlockingQueue<Runnable> workQueue,
  	// 用于设置创建线程的工厂，可以给创建的线程设置特定名字，方便排查问题
  	ThreadFactory threadFactory,
  	// 线程池的饱和策略事件，有4种类型
  	RejectedExecutionHandler handler) 
```

------

**线程的执行**

![v2-b4ac2671e911a2949d0f9dca0cbf8e52_r](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-b4ac2671e911a2949d0f9dca0cbf8e52_r.png)

![v2-2ad5aec2d388d32fe520194ef4d88ceb_1440w](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-2ad5aec2d388d32fe520194ef4d88ceb_1440w.png)

**KeepAliveTime**: 用来控制，**超出corePoolSize**的线程在这个时间还没有可执行的任务，则自动销毁。

- 提交一个任务，线程池里存活的核心线程数小于线程数corePoolSize时，线程池会创建一个核心线程去处理提交的任务。
- 如果线程池核心线程数已满，即线程数已经等于corePoolSize,一个新提交的任务，会被放进任务队列workQueue排队等待执行。
- 当线程池里存活的线程数已经等于corePoolSize，并且任务队列workQueue也满，判断线程数是否达到maximumPoolSize,即最大线程数是否已满，如果没到达，创建一个非核心线程执行提交的任务。
- 如果当前的线程数到达了maxiumPoolSize,还有新任务过来，直接采用拒绝策略处理。

------

**四种拒绝策略**

- AbortPolicy(抛出一个**异常**，默认采用)
- DiscardPolicy(**直接丢弃**任务)
- DiscardOldestPolicy(从任务队列种**弹出最先**加入的任务，空出一个位置，然后再次执行execute方法把任务**加入队列**。)
- CallerRunsPolicy(如果添加到线程池失败，那么**主线程**会**自己去执行**该任务，主线程就被**阻塞**) 

------

**阻塞队列**

阻塞队列(BlockingQueue)是一个支持**两个附加操作**的队列。

1. 支持阻塞的插入方法：当队列满时，队列会阻塞插入元素的线程，知道队列不满。
2. 支持阻塞的移除方法：当队列为空时，获取元素的线程会等待队列变为非空。

------

**线程池异常处理**

```java
       ExecutorService threadPool = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 5; i++) {
            threadPool.submit(() -> {
                System.out.println("current thread name" + Thread.currentThread().getName());
                Object object = null;
                System.out.print("result## "+object.toString());
            });
        }
运行结果：
current thread namepool-1-thread-2
current thread namepool-1-thread-5
current thread namepool-1-thread-4
current thread namepool-1-thread-1
current thread namepool-1-thread-3
```

线程池里的异常没有抛出，我们无法感知任务出现了异常。



**1.try/catch捕获异常**!

```java
        ExecutorService threadPool = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 5; i++) {
            threadPool.submit(() -> {
                System.out.println("current thread name" + Thread.currentThread().getName());
                try {
                    Object object = null;
                    System.out.print("result## " + object.toString());
                }catch (Exception e){
                    System.out.println("程序出异常了");
                }
            });
        }
运行结果：
current thread namepool-1-thread-2
程序出异常了
current thread namepool-1-thread-1
程序出异常了
current thread namepool-1-thread-5
程序出异常了
current thread namepool-1-thread-3
程序出异常了
current thread namepool-1-thread-4
程序出异常了
```

**2.Future捕获异常**

```java
        ExecutorService threadPool = Executors.newFixedThreadPool(5);
        for (int i = 0; i < 5; i++) {
            Future future = threadPool.submit(() -> {
                System.out.println("current thread name" + Thread.currentThread().getName());
                Object object = null;
                System.out.print("result## " + object.toString());
            });
            try{
                future.get();
            }catch (Exception e){
                System.out.println("发生异常");
            }
        }
current thread namepool-1-thread-1
发生异常
current thread namepool-1-thread-2
发生异常
current thread namepool-1-thread-3
发生异常
current thread namepool-1-thread-4
发生异常
current thread namepool-1-thread-5
发生异常
```

**3**.重写**ThreadPoolExecutor.afterExecute()**方法

```java
class ExtendedExecutor extends ThreadPoolExecutor {
    // 这可是jdk文档里面给的例子。。
    protected void afterExecute(Runnable r, Throwable t) {
        super.afterExecute(r, t);
        if (t == null && r instanceof Future<?>) {
            try {
                Object result = ((Future<?>) r).get();
            } catch (CancellationException ce) {
                t = ce;
            } catch (ExecutionException ee) {
                t = ee.getCause();
            } catch (InterruptedException ie) {
                Thread.currentThread().interrupt(); // ignore/reset
            }
        }
        if (t != null)
            System.out.println(t);
    }
}}
```

**4**.实例化Thread时，传入自己的ThreadFactory，设置**Thread.UncaughtExceptionHandler**处理未检测的异常。

```java
ExecutorService threadPool = Executors.newFixedThreadPool(1, r -> {
            Thread t = new Thread(r);
            t.setUncaughtExceptionHandler(
                    (t1, e) -> {
                        System.out.println(t1.getName() + "线程抛出的异常"+e);
                    });
            return t;
           });
        threadPool.execute(()->{
            Object object = null;
            System.out.print("result## " + object.toString());
        });
```

**线程池异常处理4个方式**

![v2-4a46342775c6f9ad8543654890833b6b_720w](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-4a46342775c6f9ad8543654890833b6b_720w.jpg)

------

**线程池的工作队列**

- **ArrayBlockingQueue(有界队列)**：是一个用**数组**实现的**有界阻塞队列**，FIFO排序。
- **LinkedBlockingQueue(可设置容量队列)**：基于**链表结构的阻塞队列**，FIFO，容量选择性设置，**不设置**的话是一个**无边界的阻塞队列**，最大长度为Integer.MAX_VALUE,**吞吐量通常高**于ArrayBlockingQueue; **newFixedThreadPool**线程使用了这个队列。
- **DelayQueue(延迟队列)**: **任务定时周期**的**延迟执行**的队列。根据**指定执行时间从小到大排序**，**否则**根据**插入先后排序**。 **newScheduledThreadPool**线程池使用了这个队列。
- **PriorityBlockingQueue(优先级队列)**：具有**优先级的无界阻塞**队列。
- **SynchronousQueue(同步队列)**：**不存储元素的阻塞队列**，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于**阻塞状态**。 **newCachedThreadPool**使用了这个队列。

------

**常用线程池**

- newFixedThreadPool				固定数目线程的线程池
- newCachedTheadPool              可缓存线程的线程池
- newSingleThreadExecutor        单线程的线程池
- newScheduledThreadPool        定时及周期执行的线程池

------

**newFixedThreadPool**

```java
  public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>(),
                                      threadFactory);
  }
```

**特点**：

- 核心线程数和最大线程数一样
- 没有所谓的非空闲时间，即keepAliveTime为0
- 阻塞队列为无界队列LinkedBlockingQueue

**工作机制**：

![v2-c79663b197b24233f1e0c0a15099b5dd_720w](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-c79663b197b24233f1e0c0a15099b5dd_720w.jpg)

- 提交任务
- 如果线程数少于核心数，创建核心线程执行任务
- 如果线程数等于核心数，把任务添加到LinkedBlockingQueue队列
- 如果线程执行完任务，去阻塞队列取任务，继续执行。



**实例代码**

```java
ExecutorService executor = Executors.newFixedThreadPool(3);
for(int i=0;i<7;i++){
    executor.execute(() -> {
        try {
            System.out.println(Thread.currentThread().getName()+"正在执行 "+System.currentTimeMillis());
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
}
    /**
     pool-1-thread-1正在执行 1606129456710
     pool-1-thread-3正在执行 1606129456710
     pool-1-thread-2正在执行 1606129456710
     pool-1-thread-2正在执行 1606129457713
     pool-1-thread-1正在执行 1606129457713
     pool-1-thread-3正在执行 1606129457713
     pool-1-thread-1正在执行 1606129458718
     */
// 分组 按最大核心数3分组运行，一共运行了3次， 3-3-1
```



**面试题：使用无界队列的线程池会导致内存飙升？**

答案： 会的，newFixedThreadPool使用了**无界的阻塞队列LinkedBlockingQueue**,如果线程获取了一个任务后，任务的执行时间比较长，会导致队列的任务越积越多，机器内存使用不停飙升，最终导致**OOM**(OutOfMemory)。



**使用场景**：

**FixedThreadPool**适用于处理**CPU密集型的任务**，确保CPU在长期被工作线程使用的情况下，**尽可能的少的分配线程**，即适用**执行长期的任务**。



------

**newCachedThreadPool**

```java
   public static ExecutorService newCachedThreadPool(ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>(),
                                      threadFactory);
    }
```

**特点：**

- 核心线程数为0
- 最大线程数为Integer.MAX_VALUE
- 阻塞队列是SynchronousQueue
- 非核心线程空闲存活时间为60秒

当**提交任务的速度大于处理任务的速度**时，**每次提交一个任务**，就必然会**创建一个线程**。极端情况下会创建过多的线程，耗尽CPU和存储。由于空闲60秒的线程会被终止，长时间保持空闲的CachedThreadPool不会占用任何资源。

**工作机制**

![v2-ebd1b37b1baaf31854af0de28340238f_720w](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-ebd1b37b1baaf31854af0de28340238f_720w.jpg)

- 提交任务
- 因为没有核心线程，所以任务**直接加到SynchronousQueue**队列。
- 判断是否与空闲线程，如果有，就取出任务执行。
- 如果**没有空闲线程，就新建一个线程执行**。
- **执行完任务的线程，存活60秒，如果在这期间，接到任务，可以继续活下去；否则被销毁**。

**实例代码**

```java
        ExecutorService executor = Executors.newCachedThreadPool();
        for(int i=0;i<7;i++){
            executor.execute(() -> {
                try {
                    System.out.println(Thread.currentThread().getName()+ " 正在执行"+" "+System.currentTimeMillis());
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
    /**
     pool-1-thread-1 正在执行 1606129376409
     pool-1-thread-3 正在执行 1606129376409
     pool-1-thread-2 正在执行 1606129376409
     pool-1-thread-4 正在执行 1606129376409
     pool-1-thread-5 正在执行 1606129376409
     pool-1-thread-6 正在执行 1606129376409
     pool-1-thread-7 正在执行 1606129376410
     */
// 全部同时并发，不同线程，线程结束后会保留60秒，重复利用
```



**使用场景**

用于**并发**执行**大量短期**的**小任务**。



------

**newSingleThreadExecutor**

```java
  public static ExecutorService newSingleThreadExecutor(ThreadFactory threadFactory) {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>(),
                                    threadFactory));
    }
```

**线程池特点**

- 核心线程数1
- 最大线程数1
- 阻塞队列是LinkedBlockingQueue
- keepAliveTime为0

**工作机制**

![v2-a18e8b8a21853de2295d43ec10f843a6_720w](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-a18e8b8a21853de2295d43ec10f843a6_720w.jpg)

- 提交任务
- 线程池是否有一条线程，如果没有，新建线程执行任务
- 如果有，将任务添加到阻塞队列
- 当前的唯一线程，从队列取任务，执行完一个，再继续取。

```java
        ExecutorService executor = Executors.newSingleThreadExecutor();
        for(int i=0;i<7;i++){
            executor.execute(() -> {
                try {
                    System.out.println(Thread.currentThread().getName()+ " 正在执行"+" "+System.currentTimeMillis());
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
    /**
     pool-1-thread-1 正在执行 1606129871131
     pool-1-thread-1 正在执行 1606129872134
     pool-1-thread-1 正在执行 1606129873138
     pool-1-thread-1 正在执行 1606129874142
     pool-1-thread-1 正在执行 1606129875147
     pool-1-thread-1 正在执行 1606129876150
     pool-1-thread-1 正在执行 1606129877154
     */
// 由结果可见，单线程执行了7次
```



**使用场景**

适用于**串行**执行任务，一个接一个执行。



------

**newScheduledThreadPool**

```java
    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, Integer.MAX_VALUE, 0, NANOSECONDS,
              new DelayedWorkQueue());
    }
```

**线程池特点**

- 最大线程数Integer.MAX_VALUE
- 阻塞队列是DelayedWorkQueue
- keepAliveTime为0
- scheduleAtFixedRate(): 按某种速率周期执行
- scheduleWithFixedDelay():在某个延迟后执行

**工作机制**

- 添加一个任务
- 线程池中线程从**DelayQueue**中取任务
- 线程从DelayQueue中取**time大于等于当前时间**的task
- 执行完后**修改这个task的time**为下次执行的时间
- 这个**task放回DelayQueue队列**中

```java
static void newScheduledThreadPoolExample(){
    ScheduledExecutorService s = Executors.newScheduledThreadPool(1);
    s.scheduleWithFixedDelay(() -> {
        System.out.println(Thread.currentThread().getName()+ " 正在执行"+" "+System.currentTimeMillis());
    },0,3,TimeUnit.SECONDS);
      /**
     pool-1-thread-1 正在执行 1606132425243
     pool-1-thread-1 正在执行 1606132428246
     pool-1-thread-1 正在执行 1606132431248
     pool-1-thread-1 正在执行 1606132434251
     */
  // 由结果可见，运行间隔为设置的时间
```



**使用场景**

**周期性执行任务**的场景，需要限制线程数量的场景。

------

#### 45.线程池的状态

- RUNNING

  - 该状态的线程池会接收新任务，并处理阻塞队列中的任务；
  - 调用线程池的shutdown()方法，切换到SHUTDOWN状态；
  - 调用showdownNow()方法，切换到STOP状态；

- SHUTDOWN: 等待线程池和队列中的任务执行完成后进入Tidying状态

- STOP: 等待线程池中的任务执行完成后进入Tidying

- TIDYING: 任务已经运行终止

- TERMINATED：terminated()函数执行完成后，进入TERMINATED

  ![v2-b5783bcc607e99e365c16f791c4dfedd_r](/Users/guohuanjie/Documents/programming/LearnNote/image/v2-b5783bcc607e99e365c16f791c4dfedd_r.jpg)



#### 46. 线程池中submit()和execute()方法有什么区别？

- **execute**(): 只能执行**Runnable**任务
- **submit**(): 可以执行**Runnable和Callable**任务

#### 47.在JAVA程序中如何保证多线程的运行安全？

1. 使用**安全类**，比如Java.util.**concurrent**下的类。
2. 使用**自动锁synchronized**。
3. 使用**手动锁Lock**。



**手动锁**

```Java
Lock lock = new ReentrantLock();
lock. lock();
try {
    System. out. println("获得锁");
} catch (Exception e) {
    // TODO: handle exception
} finally {
    System. out. println("释放锁");
    lock. unlock();
}
```

#### 48.多线程中synchronized锁升级的原理是什么？

synchronized锁升级原理：在锁对象的**对象头**里面有一个**threadid**字段，第一次访问时threadid为空，jvm让其**持有偏向锁**，并将**threadid设置为其线程id**,再次进入的时候会先判断threadid是否与其线程一致，**如果一致则可以直接使用此对象**，如果**不一致，则升级偏向锁为轻量级锁**，通过自旋循环一定次数来获取锁，执行一定次数后，如果**还没有正常获取到要使用的对象**，此时就会把锁**从轻量级升级为重量级**，此过程就构成了synchronized锁升级。



锁的升级的目的：锁升级是为了降低锁带来的性能消耗。使用**偏向锁升级为轻量级锁在升级到重量级锁**的方式，**降低了锁带来的性能消耗**。

------

```java
public class SynchronizedExample implements Runnable{
    // 共享资源
    static int i=0;
    synchronized public  void increase(){
        i++;
    }

    @Override
    public void run() {
        for(int j =0;j<1000000;j++){
            increase();
        }
    }

    public static void main(String[] args) {
        SynchronizedExample sy = new SynchronizedExample();
        Thread t1 = new Thread(sy);
        Thread t2 = new Thread(sy);
        t1.start();
        t2.start();
        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        // 去掉synchronize， 1115745
        // 加上  2000000
        System.out.println(i);
    }
}
```

#### 49.什么是死锁

当线程**A持有独占锁a,并尝试去获取独占锁b**的同时，线程**B持有独占锁b，并尝试获取独占锁a**的情况下，就会发生**AB两个线程由于持有对方需要的锁**，而发生**阻塞**现象，我们称之为**死锁**。

#### 50.怎么防止死锁

- 尽量使用**tryLock**(long timeout,TimeUnit unit)的方法(ReentrantLock,ReentrantReadWriteLock),设置**超时时间**，**超时可以退出防止死锁**。
- 尽量使用**Java.util.concurrent并发类**替代自己手写锁。
- 尽量**降低锁的使用粒度**，**不要几个功能用同一把锁**。
- 尽量**减少同步代码块**。

#### 51. ThreadLocal是什么？有哪些使用场景？

ThreadLocal为每个使用该变量的线程提供**独立**的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。

ThreadLocal经典使用场景是数据库连接和session管理等。

#### 52.说一下synchronized底层实现原理？

synchronized是由一对**monitorenter**/**monitorexit**指令实现的，monitor对象是同步的**基本实现单元**。在Java6之前，monitor的实现完全是依靠操作系统内部的**互斥锁**，因为需要进行用户态到内核态的切换，所以同步操作是一个无差别的重量级操作。后面提供了三种不同的monitor实现：**偏向锁**(Biased Locking)、**轻量级锁**和**重量级锁**。

#### 53.synchronized和volatile的区别？

- volatile是**变量修饰符**；synchronized是修饰**类、方法、代码块**。
- volatile仅能实现变量的**修改可见性**，**不能保证原子性**；而synchronized可以**保证变量的修改可见性和原子性**。
- volatile**不会**造成线程的**阻塞**；synchronized可能造成**线程阻塞**。

#### 54.synchronized和Lock有什么区别？

- synchronized可以给**类、方法、代码块**加锁；而lock只能给**代码块**加锁。
- synchronized**不需要手动获取锁和释放锁**，使用简单，发生异常会**自动释放锁**，不会造成死锁；而lock**需要自己加锁和释放锁**，如果使用不当没有**unLock()**去释放锁可能会造成死锁。
- 通过Lock可以知道**有没有成功获取锁**，而synchronized却无法办到。

#### 55.synchronized和ReentrantLock区别是什么？

- ReentrantLock使用比较灵活，但是必须有**释放锁**的配合动作；
- ReentrantLock必须**手动获取与释放**，而synchronized**不需要手动释放和开启锁**；
- ReentrantLock只适用于**代码块锁**，而synchronized可用于修饰**方法、代码块等**。

#### 56.说一下atomic的原理？

atomic主要利用**CAS**(Compare And Wwap)和**volatile**和**native**方法来保证原子操作，从而避免synchronized的高开销，执行效率大为提升。

### 反射

#### 57.什么是反射？

反射是在**运动**状态中，对于任意一个类，都能够知道这个**类所有属性和方法**；对于任意一个对象，都能调用它的任意一个**方法和属性**；这种动态获取的信息以及**动态调用对象**的方法的功能成为java语言的**反射机制**。

#### 58.什么是Java序列化？什么情况下需要序列化？

Java序列化是为了保持各种对象在内存中的状态，并且可以把保存的对象再读出来。

以下情况需要使用Java序列化：

- 想把**内存**中的对象状态保存到一个**文件**中或者**数据库**中时候；
- 想用套接字在**网络上传送**对象的时候；
- 想通过**RMI**(远程方法调用)传输对象的时候。

#### 59.动态代理是什么？有哪些应用？

动态代理的应用有Spring aop、hibernate数据查询、测试框架的后端mock、rpc， Java注解对象获取等。

#### 60.怎么实现动态代理？

**JDK原生动态代理**和**cglib动态代理**。JDK原生动态代理是基于接口实现的，而cglib是基于继承当前类的子类实现的。

------

### 对象拷贝

#### 61.为什么要使用克隆？

克隆的对象可能包含一些已经**修改过的属性**，而new出来的对象的属性都还是**初始化**的值，所以当需要一个新的对象来**保存当前的“状态”**就靠克隆方法。

#### 62.如何实现对象克隆？

- 实现**Cloneable**接口并**重写**Object类中的clone()方法。
- 实现**Serializable**接口，通过对象的**序列化**和**反序列化**实现克隆，可以实现真正的深度克隆。

#### 63.深拷贝和浅拷贝区别是什么？

- 浅克隆：当前对象被复制时**只复制它本身**和其中**包含的值类型的成员变量**，而**引用类型的成员对象并没有复制**。 (深拷贝被引用的对象也会生成一份新的！)
- 深克隆：出了对象本身被复制外，对象所包含的所有成员变量也将复制。

------

### Java Web

#### 64.JSP和servlet有什么区别？

JSP是servlet技术的扩展，本质上就是servlet的简易方式。servlet和JSP最主要的不同点在于，servlet的应用逻辑是在Java文件中，并且**完全**从表示层中的html里**分离开来**，而**JSP**的情况是**Java和html可以组合**成一个扩展名为JSP的文件。JSP注重**视图**，servlet主要用于**控制逻辑**。

#### 65.JSP有哪些内置对象？作用分别是什么？

JSP有9大内置对象：

- request:封装客户端的请求，其中包含来自get和post请求的参数；
- response：封装服务器对客户端的响应；
- pageContext：通过该对象可以获取其它对象。
- session：封装用户会话的对象。
- application：封装服务器运行环境的对象；
- out：输出服务器响应的输出流对象；
- config：Web应用的配置对象；
- page：JSP面页本身（相当于Java中的this）；
- exception：封装页面抛出异常的对象。

#### 66.说一下JSP的4种作用域？

- page：代表与一个面页相关的对象和属性。
- request：代表与客户端发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个web组件；需要在页面显示的临时数据可以置于此作用域。
- session：代表与某个用户与服务器建立的一次会话相关的对象和属性。跟某个用户相关的数据应该放在用户自己的session中。
- application：代表与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域。

#### 67.session和cookie有什么区别？

- 存储位置不同：session存储在服务器端；cookie存储在浏览器端。
- 安全性不同：cookie安全性一般，在浏览器存储，可以被伪造和修改。
- 容量和个数现在：cookie有荣耀限制，每个站点下的cookie也有个数限制。
- 存储的多样性：session可以储存在Redis中、数据库中、应用程序中；而cookie只能存储在浏览器中。

#### 68.说一下session的工作原理？

session的工作原理是客户端登陆完成之后，服务器会创建对于的session，session创建完之后，会把session的id发送给客户端，客户端再存储到浏览器中。这样客户端每次访问浏览器时，都带着sessionid,服务器拿到sessionid之后，在内存找到与之对应的session这样就可以正常工作了。

#### 69.如果客户端禁止cookie能实现session还能用吗？

可以用，session只是以来cookie存储sessionID，如果cookie被禁用了，可以使用URL中添加sessionID的方式保证session能正常使用。

#### 70.Spring mvc和struts的区别是什么？

- 拦截级别：struts2时类级别的拦截；spring mvc时方法级别的拦截。
- 数据独立性：Spring mvc的方法之间基本上独立，独享request和response数据，请求数据通过参数获取，处理结果通过ModelMap交回给框架，方法之间不共享变量；而struts2虽然方法之间也是独立的，但其所有action变量时共享的，这不会影响程序运行，却给我们编码和读程序时带来一定麻烦。
- 拦截机制：struts2有以自己的interceptor机制，spring mvc用的是独立的aop方式，这样导致struts2的配置文件量比spring mvc大。
- 对ajax的支持：spring mvc集成了ajax,所有ajax使用很方便，只需要一个注解@ResponseBody就可以实现了；而struts2一般需要安装插件或者自己写代码才行。

#### 71.如何避免SQL注入？

- 使用**预处理PreparedStatement**。
- 使用**正则表达式过滤**掉字符串中的特殊字符。

#### 72.什么是XSS攻击，如何避免？

XSS攻击：即跨站脚本攻击，它是Web程序中常见的漏洞。原理是攻击者往Web页面里插入恶意的脚本代码（css代码、JavaScript代码等），当用户浏览该页面时，嵌入其中的脚本代码会被执行，从而达到恶意攻击用户的目的，如盗取用户cookie、破坏页面结构、重定向到其他网站等。

#### 73. 什么是CSRF攻击，如何避免？

CSRF：Cross-Site Request Forgery（跨站攻击伪造），可以理解为攻击者盗用了你的身份，以你的名义发送恶意请求，比如：以你名义发送邮件、发消息、购买商品等。

防御手段：

- 验证请求来源地址；
- 关键操作添加验证码；
- 在请求地址添加token并验证。 

------

### 异常

#### 74.throw和throws的区别？

- throw：是**真实抛出**一个异常。（可在函数里直接人为抛出）
- throws：是**声明**可能会抛出一个异常。（在函数结尾声明可能抛出）

#### 75.final、finally、finalize有什么区别？

- final：是修饰符，如果修饰类，此类**不能被继承**；如果修饰方法和变量，则表示方法和变量**不能再被改变**，只能使用。
- finally：是try{} catch{} finally{}最后一部分，表示无论发生什么情况都会执行，finally部分可以省略。
- finalize：是**Object**类的一个方法，在**垃圾收集器**执行的时候会**调用被回收对象的此方法**。

#### 76.try-catch-finally中哪个部分可以省略？

**catch和finally**都可以被省略，但是**不能同时省略**，也就是说有try的时候，必须跟一个catch或finally。

#### 77.try- catch- finally中，如果catch中return了，finally还会执行吗？

finally**一定会**执行，即使是catch中return了，catch中的return会等finally中代码执行完之后，才执行。

#### 78.常见的异常类有哪些？

- NullPointerException      空指针异常
- ClassNotFoundException 指定类不存在
- NumberFormatException   字符串转换为数字异常
- IndexOutOfBoundsException
- ClassCastException 数据类型转换异常
- FileNotFoundException 
- NoSuchMethodException
- IOException
- SocketException

------

### 网络

#### 79.http响应码301和302代表的是什么？有什么区别？

301:**永久**重定向。

302:**暂时**重定向。

他们的区别是，301对搜索引擎优化（SEO）更加有利；302有被提示为网络拦截的风险。

#### 80.forward和redirect的区别？

forward是**转发**和redirect是**重定向**：

- 地址栏URL显示：forward不会发生改变，redirect url会发生改变；
- 数据共享：forward可以共享request里的数据，redirect不能共享；
- 效率：forward比redirect效率高。

#### 81.简述tcp和udp的区别？

tcp和udp是OSI模型中的**运输层**协议。tcp提供**可靠**的通信传输，而udp则常被用于让**广播**和细节控制交给应用的通信传输。

两者区别大致如下：

- tcp面向**连接**，udp面向**非连接**即发送数据前不需要建立连接；
- tcp提供**可靠**的服务（数据传输）,udp无法保证。
- tcp面向**字节流**，udp面向**报文**。
- tcp**数据传输慢**，udp**数据传输快**；

#### 82.tcp为什么要三次握手，两次不行吗？为什么？

如果采用两次握手，那么只要服务器发送确认数据包就会建立连接，但由于客户端此时并未响应服务器端的请求，那此时服务器端就会一直等待客户端，这样服务器端就白白**浪费了一定的资源**。采用三次握手，服务器端没有收到来自**客户端**的**再次确认**，则就会知道客户端并没有要求建立请求，就不会浪费服务器资源。

#### 83.说一下tcp粘包是怎么产生的？

tcp粘包可能发生在发送端或接收端：

- 发送端粘包：发送端需要等缓冲区满才发送出去，造成粘包；
- 接收方粘包：接收方不及时接收缓冲区的包，造成多个包接收。

#### 84.OSI的七层模型都有哪些？

- 物理层： **比特流**的透明传输。
- 数据链路层
- 网络层：通过路由选择算法，选择最适当的路径。
- 传输层：向用户提供可靠的端到端的差错和流量控制。
- 会话层：向两个实体的表示层提供建立和使用连接的方法。
- 表示层：处理用户信息的表示问题，如编码、数据格式转换和加密解密等。
- 应用层：直接向用户提供，完成用户希望在网络上完成的各种工作。

#### 85.get和post请求有哪些区别？

- **get**请求会被浏览器**主动缓存**，而post不会。
- get**传递参数**有**大小限制**，而post没有。
- post**参数传输更安全**，而get的参数会**明文显示**在url上。

#### 86.如何实现跨域？

实现跨域有以下几种方案：

- 服务器端运行跨域 设置CORS等于*；
- 在单个接口使用注解@CrossOrigin运行跨域；
- 使用jsonp跨域

#### 87.说一下JSONP实现原理？

JSON with Padding, 它是利用script标签的src连接可以访问不同源的特性，加载远程返回的“JS函数”来执行的。

------



### 设计模式

#### 88.说一下熟悉的设计模式？

- 单例模式：保证被**创建一次**，节省系统开销。
- 工厂模式（简单工厂、抽象工厂）：**解藕代码**。
- 观察者模式：定义了对象之间的一对多依赖，当一个**对象改变**时，它的所有依赖者都会收到**通知**并自动更新。
- 外观模式：提供一个统一的接口，用来访问子系统中的一群接口，外观定义了一个高层的接口，让子系统更容易使用。
- 模版方法模式：定义了一个算法的骨架，而将一些步骤延迟到子类中，模版方法使得子类可以在不改变算法结构的情况下，重新定义算法步骤。
- 状态模式：允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

#### 89.简单工厂和抽象工厂有什么区别？

- 简单工厂：用来生产同一等级结构中的任意产品，等于增加新的产品，无能为力。

- 工厂方法：用来生产同一等级结构中的固定产品，支持添加任意产品。

- 抽象工厂：用来生产不同产品族的全部产品，对于增加新的产品，无能为力；支持增加产品族。

  

**抽象工厂类实现源码**

1.苹果有红苹果青苹果等，创建苹果抽象类

```java
public abstract class FruitsApple {

	public abstract void describe();
}
```

2.创建苹果类

```java
public class Apple extends FruitsApple {
	@Override
	public void describe() {
		System.out.println("我是苹果");
	}
}
```

3.创建香蕉抽象类

```java
public abstract class FruitsBanana {
	public abstract void describe();
}
```

4.创建香蕉

```java
public class Banana extends FruitsBanana {
	@Override
	public void describe() {
		System.out.println("我是香蕉");
	}
}
```

5.创建商人工厂类

```java
public class BusinessmanAbstractFactory {
	/**
	 * 创建苹果
	 * @param name 指定创建的苹果类路径
	 * @return FruitsApple
	 * @throws InstantiationException
	 * @throws IllegalAccessException
	 * @throws ClassNotFoundException
	 */
	public static FruitsApple createApple(String name) throws InstantiationException, IllegalAccessException, ClassNotFoundException{
		if("".equals(name) || name == null){
			return null;
		}
		return (FruitsApple) Class.forName(name).newInstance();
	}
	
	/**
	 * 创建香蕉
	 * @param name 指定创建的香蕉类路径
	 * @return FruitsBanana
	 * @throws InstantiationException
	 * @throws IllegalAccessException
	 * @throws ClassNotFoundException
	 */
	public static FruitsBanana createBanana(String name) throws InstantiationException, IllegalAccessException, ClassNotFoundException{
		if("".equals(name) || name == null){
			return null;
		}
		return (FruitsBanana) Class.forName(name).newInstance();
	}
}
```

6.创建测试类

```java
public class Test {
	public static void main(String[] args) throws InstantiationException, IllegalAccessException, ClassNotFoundException {
		FruitsApple apple = BusinessmanAbstractFactory.createApple("com.newtt.Apple");
		apple.describe();
		FruitsBanana banana = BusinessmanAbstractFactory.createBanana("com.newtt.Banana");
		banana.describe();
	}
}

```





















