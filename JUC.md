### 美团的“Java锁“介绍

https://tech.meituan.com/2018/11/15/java-lock.html



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



### **并发类包**

java.util.concurrent

java.util.concurrent.atomic

java.util.concurrent.locks



### **多线程的状态**

enum Thread.State{

NEW,

RUNNABLE,

BLOCKED,

WAITING,

TIMED_WAITING,

TERMINATED

}



**状态中WAITING/TIMED_WAITING**

WAITING 一直等待，TIMED_WAITING  计时等待



**Wait/sleep 都是暂停线程，有什么区别**

- wait放开锁去等待
- sleep 持有锁等待，醒了手里还有锁



### **多线程口诀**

1. 高聚低合前提下，线程操作资源类
2. 判断/干活/通知
3. 多线程交互中，必须防止多线程的虚假唤醒！
4. 标志位



### 乐观锁悲观锁

悲观锁：当前线程一旦获取锁，其他线程只能挂起。(**ReentrantLock、synchronized**)

乐观锁：每次不加锁加锁别人不会修改，但是在更新的时候会判断一下此期间别人是否有更新这个数据。 (**版本号机制、CAS**)



### synchronized(悲观锁)

wait/notify是**Object类**的

官方文档:

As in the one argument version, interrups and spurious wakeups are possible, and this method should **always be used in a loop**

```java
synchronized(obj){
  while(<condition does not hold>) obj.wait();
  // Perform action appropriate to condition
}
```



**notify和notifyAll区别**

- 如果线程调用了对象的wait()方法，那么线程便会处于对象的**等待池**中，等待池中的线程**不会去竞争该对象的锁**。
- 当有线程调用了对象的**notifyAll()**方法(**唤醒所有wait()线程**)或**notify()**方法(**只随机唤醒一个wait线程**)，被唤醒的线程便进入该对象的锁池中，锁池中的线程会去竞争该对象的锁。
- 优先级高的线程竞争道对象锁的概率大，假若某线程**没有竞争到该对象锁**，它**还会保留在锁池**中，唯有线程再次调用wait()方法，它才会回到等待池中。而竞争道对象锁的线程则继续执行下去，直到执行完了synchronized代码块，它会释放该对象锁，这时线程会继续竞争该对象锁。



```java
/**
 题目：两个线程，可以操作初始值为零的一个变量，
 实现一个线程对该变量加1，一个减1
 交替实现，10轮

 1. 高聚低合前提下，线程操作资源类
 2. 判断/干活/通知
 3. 多线程交互中，必须要防止多线程的虚假唤醒，交互也即(wait/notify,await/signal)
 */
class AirCondition{
    private int number = 0;

    public synchronized void increment() throws Exception{
        // 多线程的判断  不许用if  只能用while！！！！
//        if(number!=0)  this.wait();
        while (number!=0) this.wait();
        number++;
        System.out.println(Thread.currentThread().getName()+'\t'+number);
        // 唤醒
        // 本处用notify可能会死锁，increment执行后，锁随机给到另一个increment
        // 或者decrement执行完成后，锁随机给到另一个decrement
        this.notifyAll();
    }
    public synchronized void decrement() throws Exception{
        // 多线程的判断  不许用if  只能用while！！！！
       // this.wait()后， 被唤醒时，再被拉回去重新判断一次，防止虚假唤醒
        while (number==0) this.wait();
        //if(number==0) this.wait();
        number--;
        System.out.println(Thread.currentThread().getName()+'\t'+number);
        //唤醒
        // 本处用notify可能会死锁，increment执行后，锁随机给到另一个increment
        // 或者decrement执行完成后，锁随机给到另一个decrement
         this.notifyAll();
    }
}
public class ThreadWaitNotifyDemo {
    public static void main(String[] args) {
        AirCondition airCondition = new AirCondition();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.increment();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"A").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.increment();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"B").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.decrement();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"C").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.decrement();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"D").start();
    }
}
```





**Synchronized用的8个问题**

```java
package com.company.juc;

import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Phone{
    // lock的实现在静态的时候有区别
//    private static Lock lock = new ReentrantLock();
//    private Condition condition= lock.newCondition();
    public synchronized void sendEmail(){
        try {
            TimeUnit.SECONDS.sleep(4);
            System.out.println("----sendEmail");
        }catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static synchronized void sendSMS(){
            System.out.println("----sendSMS");
    }
    public void hello(){
        System.out.println("hello");
    }
}

/**
 * 题目：多线程8锁
 *  1. 标准访问，请问先打印邮件还是短信？
    先打印邮件，再打印SMS
    synchronized锁的不是某个方法，而是锁住当前对象的this!!!

    2. 邮件方法sleep4秒，先打印邮件还是短信
    先打印邮件，sleep方法不释放锁！
    synchronized锁的不是某个方法，而是锁住当前对象的this!!!

    3. 新增一个普通方法hello，先打印邮件还是hello？
    hello
    普通方法没加synchronized，不会进行锁争抢

    4. 两部手机，先打印邮件还是短信
    SMS,用的不是同一把锁

    5. 两个静态同步方法，同一部手机，先邮件还是短信
    先邮件
    static 锁的是类的Class对象

    6. 两个静态同步方法，两部手机，先邮件还是短信
    先邮件
     static 锁的是类的Class对象(类似JVM方法区里的类信息或着说Class这个文件)
    锁住的是生产手机的工厂！！！


    7. 1个普通同步方法，一个静态同步方法，1部手机，先邮件还是短信
    先短信
    加了static锁的是.class文件
    普通同步锁的是类的对象
    两个锁不是同一个
    理解为，锁住生产出来的手机，锁住工厂，锁是不相干的
    但如果ReentrantLock，Lock需要声明为static 所以先邮件


     8. 1个普通同步方法，一个静态同步方法，2部手机，先邮件还是短信
     加了static锁的是.class文件
     普通同步锁的是类的对象
     两个锁不是同一个
     先短信
     但如果ReentrantLock，Lock需要声明为static 所以先邮件
 */
public class Lock8 {
    public static void main(String[] args) throws Exception{
        Phone phone1 = new Phone();
        Phone phone2 = new Phone();
        new Thread(()->{
            phone1.sendEmail();
        },"A").start();
        new Thread(()->{
            phone2.sendSMS();
        },"B").start();

    }
}

```

**synchronized笔记**

- 一个对象里面如果有多个synchronized方法，某一个时刻内，只要一个线程去调用其中的一个synchronized方法了，其它的线程都只能等待。

- 某一时间内，**只能有唯一一个线程去访问这些synchronized方法，锁的是当前对象的this**，被锁定后，其它线程都不能进入到当前对象的其它synchronized方法。

- 加个普通方法后，和同步锁无关

- synchronized实现同步的基础：Java中每一个对象都可以作为锁

  具体表现为：

  1. 对于**普通同步**方法，锁是当前**实例对象**

     锁住一部部的手机！

  2. 对于**静态同步**方法，**锁是当前类的Class对象**。(类似锁住方法区JVM的类信息,或.class文件)

     锁住生产手机的模版！

  3. 对于**同步方法块**，**锁的是synchronized括号里的配置对象**。

- 当一个线程试图访问同步代码块时，首先必须得到锁，退出或抛出异常时必须释放锁

- 一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁。

  然而，别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同锁，所以不必等待该实例的锁就可以获取自己的锁。

- 所有的静态同步方法用的是同一把锁——类本身(.Class)

  静态同步方法与非静态同步方法锁的对象不同(Class/This)，不会有竞争

  但如果一个静态同步方法获得锁后，其他静态同步方法必须等待这个方法释放锁后才能获取锁

  而不管是同一个实例对象的静态同步方法直接，还是不同实例对象的静态同步方法直接，只要是同一个类，就用的同一个锁。





### Lock(悲观锁)

Lock支持Condition对象

Lock替代了synchronized方法

Condition替代了Object monitor方法



**Lock能实现精准通知，控制多线程执行顺序**！！！



- synchronized (**wait/notify**)
- lock- Condition (**await/signal**)



```java
/**
 题目：两个线程，可以操作初始值为零的一个变量，
 实现一个线程对该变量加1，一个减1
 交替实现，10轮

 1. 高聚低合前提下，线程操作资源类
 2. 判断/干活/通知
 3. 多线程交互中，必须要防止多线程的虚假唤醒，交互也即(wait/notify,await/signal)
 4. 标志位
 */
class AirCondition{
    private int number = 0;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public void incrementUseLock() throws Exception{
        // 多线程的判断  不许用if  只能用while！！！！
//        if(number!=0)  this.wait();
        try {
            lock.lock();
            while (number != 0) condition.await();
            number++;
            System.out.println(Thread.currentThread().getName() + '\t' + number);
            // 唤醒
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }

    public void decrementUseLock() throws Exception{
        try {
            lock.lock();
            // 多线程的判断  不许用if  只能用while！！！！
            while (number == 0) condition.await();
            //if(number==0) this.wait();
            number--;
            System.out.println(Thread.currentThread().getName() + '\t' + number);
            //唤醒
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
}
public class ThreadWaitNotifyDemo {

    public static void main(String[] args) {
        AirCondition airCondition = new AirCondition();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.incrementUseLock();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"A").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.incrementUseLock();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"B").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.decrementUseLock();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"C").start();
        new Thread(()->{
            try {
                for (int i = 0; i < 10; i++) airCondition.decrementUseLock();
            }catch (Exception e){
                e.printStackTrace();
            }
        },"D").start();
    }
}
```



**Lock实现多线程精准通知**

```java
class ShareResource{
    private int number = 1; // 1:A 2:B 3:C
    private Lock lock = new ReentrantLock();
  	// 三个条件
    private Condition condition1 = lock.newCondition();
    private Condition condition2 = lock.newCondition();
    private Condition condition3 = lock.newCondition();
    public void print5()  {
        try {
            lock.lock();
            // 线程
            while (number!=1) condition1.await();
            for(int i=0;i<5;i++) System.out.println(Thread.currentThread().getName()+"\t"+i+" AA");
            // 修改标记位
            number = 2;
            // 通知线程2
            condition2.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void print10()  {
        try {
            lock.lock();
            // 线程
            while (number!=2) condition2.await();
            for(int i=0;i<10;i++) System.out.println(Thread.currentThread().getName()+"\t"+i+" BB");
            // 修改标记位
            number = 3;
            // 通知线程3
            condition3.signal();
        }catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void print15()  {
        try {
            lock.lock();
            // 线程
            while (number!=3) condition3.await();
            for(int i=0;i<15;i++) System.out.println(Thread.currentThread().getName()+"\t"+i+" CC");
            // 修改标记位
            number = 1;
            // 通知线程1
            condition1.signal();
        }catch (Exception e) {
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
/**
 多线程按顺序执行 实现A->B->C
 三个线程启动，要求如下：
 AA打印5次，BB打印10次，CC打印15次
 AA打印5次，BB打印10次，CC打印15次
 。。。执行10轮

 1. 高聚低合前提下，线程操作资源类
 2. 判断/干活/通知
 3. 多线程交互中，必须要防止多线程的虚假唤醒，交互也即(wait/notify,await/signal)
 4. 标志位
 */
public class ThreadOrderAccess {
    public static void main(String[] args) {
        ShareResource shareResource = new ShareResource();
            new Thread(() -> {
                for(int i=0;i<10;i++)
                shareResource.print5();
            },"A").start();
        new Thread(() -> {
            for(int i=0;i<10;i++)
                shareResource.print10();
        },"B").start();
        new Thread(() -> {
            for(int i=0;i<10;i++)
                shareResource.print15();
        },"C").start();
    }
}
```

------



### CAS (乐观锁)

compare and swap

CAS基于硬件实现

也称自旋锁

**CAS算法**

- 需要读写的内存值 V
- 进行比较的值A
- 拟写入的新值B

当且仅当V==A时，CAS通过原子操作用新值B来更新V的值，否则不会执行任何操作(**比较和替换是一个原子操作**)。一般情况下是一个自旋操作，即不断的重试。

本处原子操作可以考虑操作系统层面。  **关中断 -> 比较和替换 ->开中断**



Java里面的Semaphore类，就是通过CAS实现。



**CAS缺点**

1. 循环时间长开销大
2. 只能保证一个共享变量的原子操作
3. ABA问题



**什么是ABA问题？ABA问题怎么解决？**

如果一个变量V初次读取的时候是A值，并且在准备赋值的时候检查到它仍然是A值，那我们就能说明它的值没有被其他线程修改过了吗？很明显是不能的，因为在这段时间它的值可能被改为其他值，然后又改回A，那CAS操作就会误认为它从来没有被修改过。这个问题被称为CAS操作的 "ABA"问题。

JDK 1.5 以后的 AtomicStampedReference 类就提供了此种能力，其中的 **compareAndSet** 方法就是首**先检查当前引用是否等于预期引用**，**并且当前标志是否等于预期标志**，如果**全部相等**，则以原子方式将该**引用和该标志的值**设置为给定的更新值。



因为自旋开销很大，所以**CAS适用于读频繁的场景**，**ReentrantLock/synchronized适用于写频繁**场景。



------



### **不安全集合类和Map**

并发修改异常：  **java.util.ConcurrentModificationException**



#### List不安全

**ArrayList扩容为原来的1.5倍**

```java
  /**
 请举例说明集合类是不安全的
 1. 故障现象
 java.util.ConcurrentModificationException

 2. 导致原因
ArrayList 的 add方法没有加锁

 3. 解决方案
    3.1 Vector  // 底层Synchronize   能保证数据一致性，读写效率不行
    3.2 Collections.synchronizedList(new ArrayList<>())
    3.3 java.util.concurrent.CopyOnWriteArrayList 类 -> 底层 ReentrantLock
        写时复制
 4. 优化建议
 */

void ListNoSafe(){
        // List<String> list = new Vector<>();
        // List<String> list = Collections.synchronizedList(new ArrayList<>());
        // CopyOnWriteArrayList 底层 add方法  ReentrantLock
        List<String> list = new CopyOnWriteArrayList<>();

        /**
         每次都不同
         [dc90, ec1e]
         [dc90, ec1e, a3a9]
         [dc90, ec1e]

         [null, null, 348a]
         [null, null, 348a]
         [null, null, 348a]
         */

        //  java.util.ConcurrentModificationException
        for(int i=0;i<30;i++){
            new Thread(()->{
                list.add(UUID.randomUUID().toString().substring(0,4));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
```

**解决List不安全，可以有三种解决方案**

- Vector
- Collections.synchronizedList
- CopyOnWriteArrayList

------



##### Vector

**Vector文件下add方法实现**

```java
   // synchronized 关键字实现加锁
		public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }
```

------

##### Collections.synchronizedList

Collections.synchronizedList函数返回了SynchronizedList对象和SynchronizedRandomAccessList对象。

```java
    public static <T> List<T> synchronizedList(List<T> list) {
        return (list instanceof RandomAccess ?
                new SynchronizedRandomAccessList<>(list) :
                new SynchronizedList<>(list));
    }
```

SynchronizedList是SynchronizedRandomAccessList的父类，add实现方法都一样

```java
      public void add(int index, E element) {
            synchronized (mutex) {list.add(index, element);}
        }
```

通过加synchronized关键字实现对list加锁



##### CopyOnWriteArrayList

```
java.util.concurrent.CopyOnWriteArrayList
```



**CopyOnWriteArrayList文件下add方法实现**

写时复制，读写分离，在不同Object[]对象里进行读和写！

用的**ReentrantLock**实现写互斥

```java
    public boolean add(E e) {
      	// ReentrantLock
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
          // 获取老的数组对象
            Object[] elements = getArray();
            int len = elements.length;
           // n复制到n+1前面n个的槽位 扩容方法Arrays.copyOf
            Object[] newElements = Arrays.copyOf(elements, len + 1);  
          // 第n+1个槽位放新数据
            newElements[len] = e;
          // 替换掉类里的数组对象
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
```



| 方案                                          | 用到的锁           | 优点                           | 缺点             |
| --------------------------------------------- | ------------------ | ------------------------------ | ---------------- |
| Vector                                        | synchronized关键字 | 保证数据一致性                 | 读写效率低       |
| Collections.synchronizedList(new ArrayList()) | synchronized关键字 | 保证数据一致性                 | 读写效率低       |
| CopyOnWriteArrayList                          | ReentrantLock类    | 保证数据一致性，读写分离，高效 | 而外开辟内存空间 |

------



#### Set不安全



**Set的底层结构**

- HashSet底层数据结构是HashMap!
- **HashSet的add**方法**调用HashMap的put**方法，HashMap的value是一个无关紧要的**Object常量**(final)。
- HashSet把值**存在HashMap的Key**!



HashMap底层数组+链表(红黑树)



**解决Set不安全的方法**

- Collections.synchronizedSet
- CopyOnWriteArraySet

------

##### Collections.synchronizedSet

Collections.synchronizedSet方法返回SynchronizedSet对象

```java
    public static <T> Set<T> synchronizedSet(Set<T> s) {
        return new SynchronizedSet<>(s);
    }
```

SynchronizedSet继承了SynchronizedCollection

SynchronizedCollection add方法源码如下

```java
        public boolean add(E e) {
            synchronized (mutex) {return c.add(e);}
        }
```

------

##### CopyOnWriteArraySet

ReentrantLock实现

------



#### HashMap不安全

**HashMap不安全解决方案**

- Collections.synchronizedMap
- ConcurrentHashMap

------



##### Collections.synchronizedMap

Collections.synchronizedMap方法源码

```java
    public static <K,V> Map<K,V> synchronizedMap(Map<K,V> m) {
        return new SynchronizedMap<>(m);
    }
```

SynchronizedMap类里put方法源码

```java
        public V put(K key, V value) {
            synchronized (mutex) {return m.put(key, value);}
        }
```



------

##### ConcurrentHashMap

本处ConcurrentHashMap的put方法调用了putVal方法，方法里的锁用**synchronized**关键字实现

```java
    final V putVal(K key, V value, boolean onlyIfAbsent) {
        if (key == null || value == null) throw new NullPointerException();
        int hash = spread(key.hashCode());
        int binCount = 0;
        for (Node<K,V>[] tab = table;;) {
            Node<K,V> f; int n, i, fh;
            if (tab == null || (n = tab.length) == 0)
                tab = initTable();
            else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
                if (casTabAt(tab, i, null,
                             new Node<K,V>(hash, key, value, null)))
                    break;                   // no lock when adding to empty bin
            }
            else if ((fh = f.hash) == MOVED)
                tab = helpTransfer(tab, f);
            else {
                V oldVal = null;
                synchronized (f) {
                    if (tabAt(tab, i) == f) {
                        if (fh >= 0) {
                            binCount = 1;
                            for (Node<K,V> e = f;; ++binCount) {
                                K ek;
                                if (e.hash == hash &&
                                    ((ek = e.key) == key ||
                                     (ek != null && key.equals(ek)))) {
                                    oldVal = e.val;
                                    if (!onlyIfAbsent)
                                        e.val = value;
                                    break;
                                }
                                Node<K,V> pred = e;
                                if ((e = e.next) == null) {
                                    pred.next = new Node<K,V>(hash, key,
                                                              value, null);
                                    break;
                                }
                            }
                        }
                        else if (f instanceof TreeBin) {
                            Node<K,V> p;
                            binCount = 2;
                            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                           value)) != null) {
                                oldVal = p.val;
                                if (!onlyIfAbsent)
                                    p.val = value;
                            }
                        }
                    }
                }
                if (binCount != 0) {
                    if (binCount >= TREEIFY_THRESHOLD)
                        treeifyBin(tab, i);
                    if (oldVal != null)
                        return oldVal;
                    break;
                }
            }
        }
        addCount(1L, binCount);
        return null;
    }
```



------

### Callable

```java
java.util.concurrent.Callable; // 并发包下的Callable
java.lang.Runnable  // Runnable是lang包下的
```

新建线程的三种方式：

- 继承Thread。 Thread实现了Runnable接口
- 实现Runnable接口
- 实现Callable接口



**Callable接口与Runnable接口对比**

- Callable需要实现call接口，Runnable需要实现run接口
- call方法有抛出异常，run方法没有异常
- call方法有返回值, run方法没返回值



Callable有返回值，可以有很多用法，比如告诉程序线程是否成功执行。



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go1hy7r2vpj30js0m4n8x.jpg" alt="WeChat655501d451d7ac8d12da6a2b5b3dc59b.png" style="zoom:33%;" />

FutureTask可以通过Callable作为参数构建

FutureTask实现了RunnableFuture接口

RunnableFuture接口继承了Runnable接口

此时Callable通过充当构造参数生成实现了Runnable接口的FutureTask类



```java
class MyThread2 implements Callable<Integer>{

    @Override
    // 本处返回值为类声明时继承Callable的范型
    public Integer call() throws Exception {
        TimeUnit.SECONDS.sleep(4);
        System.out.println("come here");
        return 1024;
    }
}
// 有点类似于实现异步！
public class CallableDemo {
    public static void main(String[] args) throws Exception{
        // callable的实现
        FutureTask futureTask = new FutureTask(new MyThread2());
        // 只出现了come here一次，FutureTask会判断state是不是NEW
        new Thread(futureTask,"A").start();
        new Thread(futureTask,"B").start();
        System.out.println(Thread.currentThread().getName()+"计算完成");
     		// 本处会get方法阻塞线程
        System.out.println(futureTask.get());
        System.out.println("总的结束");
    }
}
```

FutureTask只能调用一次run,源码中会判断如果state!=NEW，直接结束。

```java
    public void run() {
        if (state != NEW ||
            !UNSAFE.compareAndSwapObject(this, runnerOffset,
                                         null, Thread.currentThread()))
            return;
        ...
    }
```



1. Callable的get方法一般放在最后一行
2. get方法会阻塞当前线程。



### CountDownLatch(CAS)

```java
java.util.concurrent.CountDownLatch; //并发包里的CountDownLatch类
import java.util.concurrent.FutureTask; // 并发包里的Future Task
```

- CountDownLatch主要有两个方法，当一个或多个线程调用await方法是，这些线程会阻塞
- 其它线程调用countDown方法会将计数器减1 (调用countDown方法的线程不会阻塞)
- 当计数器的值变为0，因await方法阻塞的线程被唤醒，继续执行

```java
public class CountDownLatchDemo {
    public static void main(String[] args) throws Exception{
        errorMethod();
        System.out.println(Thread.currentThread().getName()+"\t班长关门走人");
    }

    /**
     0	离开教室
     main	班长关门走人
     3	离开教室
     2	离开教室
     1	离开教室
     5	离开教室
     4	离开教室
     */
    static void errorMethod(){
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "\t离开教室");
            }, String.valueOf(i)).start();
        }
    }

    /**
     1	离开教室
     4	离开教室
     3	离开教室
     0	离开教室
     2	离开教室
     5	离开教室
     main	班长关门走人
     */
    static void correctMethod() throws InterruptedException {
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "\t离开教室");
                countDownLatch.countDown();
            }, String.valueOf(i)).start();
        }
        // 阻塞等待归0
        countDownLatch.await();
    }
}
```

### CyclicBarrier (ReentrantLock)

```java
java.util.concurrent.CyclicBarrier
```

**和CountDownLatch相反**，CyclicBarrier从0开始，累计到设置的值，执行对应操作。

```java
/**
 0	进入教室
 2	进入教室
 1	进入教室
 3	进入教室
 4	进入教室
 5	进入教室
 开始开会
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) throws Exception{
        CyclicBarrier cyclicBarrier = new CyclicBarrier(6,()->{
            System.out.println("开始开会");
        });
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "\t进入教室");
                try {
                    cyclicBarrier.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }, String.valueOf(i)).start();
        }
    }
}
```

### Semaphore(CAS)

信号量,**控制多线程的并发数**

```java
java.util.concurrent.Semaphore;  //并发类里面的
```

```java
new Semaphore(int permits); // 构建方法，参数为信号量的数量
```



**操作**

- acquire (获取)

  当一个线程调用acquire，它要么通过成功获取信号量(信号量减1)，要么一直等下去，直到有线程释放信号量，或超时。

- release (释放)

  实际上会将信号量的值加1，然后唤醒等待线程



**信号量的目的**

- 用于多个共享资源的互斥使用
- 并发线程数的控制

```java
/**
0	抢占到了车位
1	抢占到了车位
0	离开了车位
1	离开了车位
2	抢占到了车位
3	抢占到了车位
2	离开了车位
4	抢占到了车位
3	离开了车位
4	离开了车位
**/
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore  semaphore = new Semaphore(2); //模拟资源类，有2个空车位
        for (int i = 0; i < 5; i++) {
            new Thread(()->{
                try {
                    // 请求资源
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"\t抢占到了车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName()+"\t离开了车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    // finally中释放资源
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

当Semaphore等于1，相等于互斥锁！ synchronized和ReentrantLock实现的功能都能用Semaphore实现。

------

### ReadWriteLock

```java
public interface ReadWriteLock {
    /**
     * Returns the lock used for reading.
     *
     * @return the lock used for reading
     */
    Lock readLock();

    /**
     * Returns the lock used for writing.
     *
     * @return the lock used for writing
     */
    Lock writeLock();
}
```

普通的Lock锁，读写全锁。

读可以不加锁，写才加锁。



**思想**

- 读-读  共享
- 读-写  独占
- 写-写  独占

```mysql
class MyCache{
    private volatile Map<Integer,Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(int key, Object value){
        try {
            // 写操作加写锁
            readWriteLock.writeLock().lock();
            System.out.println(Thread.currentThread().getName() + "\t写入开始" + key);
            try {
                TimeUnit.MICROSECONDS.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "\t写入成功");
        }finally {
            // 释放写锁
            readWriteLock.writeLock().unlock();
        }
    }
    public void get(int key){
        try {
            // 读操作加读锁
            readWriteLock.readLock().lock();
            System.out.println(Thread.currentThread().getName() + "\t读取开始");
            try {
                TimeUnit.MICROSECONDS.sleep(300);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            Object obj = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取成功" + obj);
        }finally {
            // 释放读锁
            readWriteLock.readLock().lock();
        }
    }
}
public class ReadWriteLockDemo {
    public static void main(String[] args) {

        MyCache myCache = new MyCache();
        for (int i = 0; i <=5 ; i++) {
            final int temp = i;
            new Thread(() -> {
                myCache.put(temp,temp);
            },String.valueOf(i)).start();
        }

        for (int i = 0; i <=5 ; i++) {
            final int temp = i;
            new Thread(() -> {
                myCache.get(temp);
            },String.valueOf(i)).start();
        }
    }
}
```

------

### Atomic(CAS)

```java
java.util.concurrent.atomic.AtomicInteger; // current.atomic包下
```

通过调用CAS实现原子操作

```java
/**
 基于CAS实现
 */
public class AtomicClassDemo {
    public static AtomicInteger atomicInteger = new AtomicInteger(10);
    //public static int normalInt = 10;
    public static void main(String[] args) {
        new Thread(()->{
            while (true) {
                 //System.out.println(Thread.currentThread().getName() + " +1后 " + normalInt++);
                System.out.println(Thread.currentThread().getName() + " +1后 " + atomicInteger.getAndIncrement());
            }
        },"线程A").start();
        new Thread(()->{
            while (true) {
                //System.out.println(Thread.currentThread().getName() + " -1后 " + normalInt--);
                System.out.println(Thread.currentThread().getName() + " -1后 " + atomicInteger.getAndDecrement());
            }
        },"线程B").start();
    }
}
```

------

### BlockingQueue

```java
java.util.concurrent.BlockingQueue  //接口在concurrent包下
```

BlockingQueue是接口

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go2yqafmptj30z60c2k3w.jpg" alt="WeChat2c11b4001d809d8fe125c98a9ae576de.png" style="zoom:50%;" />



- 当队列是空的，从队列**获取**元素的操作就会被阻塞
- 当队列是满的，向队列**添加**元素的操作就会被阻塞



在多线程领域：所谓阻塞，在某些情况下会**挂起**线程(即阻塞),一旦条件满足，被挂起的线程又会自动**被唤起**。



**为什么需要BlockingQueue?**

好处是我们**不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程**，因为这一切BlockingQueue帮我们处理。

在concurrent包发布以前，多线程环境下，我们**需要自己去控制这些细节，尤其还要兼顾效率和线程安全**，而这会给程序带来不小的复杂度。



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go2z61uicdj30ts0x4hbg.jpg" alt="WeChat15d9f9120299740535c458a18b214f6f.png" style="zoom:33%;" />



**实现了BlockingQueue接口的类有：**

- **ArrayBlockingQueue(重要)**

  由**数组**结构组成的有界阻塞队列

- **LinkedBlockingQueue(重要)**

  由**链表**结构组成的有界(但大小默认值为integer.MAX_VALUE)阻塞队列

- DelayQueue

  使用优先级队列实现的延迟无界阻塞队列

- LinkedBlocking**Deque**

  (De=Double,双向)

  由链表组成的双向阻塞队列。

- LinkedTransferQueue

  由链表组成的无界阻塞队列

- PriorityBlockingQueue

  支持优先级排序的无界阻塞队列

- **SynchronousQueue(重要)**

  **不存储元素**的阻塞队列，也即单个元素的队列。



**BlockingQueue核心方法**

| 方法类型 | 抛出异常  | 特殊值   | 阻塞   | 超时               |
| -------- | --------- | -------- | ------ | ------------------ |
| 插入     | add(e)    | offer(e) | put(e) | offer(e,time,unit) |
| 移除     | remove()  | poll()   | take() | poll(time,unit)    |
| 检查     | element() | peek()   | 不可用 | 不可用             |

| 抛出异常 | 当阻塞队列满时，再往队列里add插入元素会抛IllegalStateException:Queue full |
| -------- | ------------------------------------------------------------ |
|          | 当阻塞队列空时，再往队列里remove移除元素会抛NoSuchElementException |
| 特殊值   | 插入方法，成功true失败false                                  |
|          | 移除方法，成功返回出队列的元素，队列里没有就返回null         |
| 一直阻塞 | 当阻塞队列满时，生产者继续往队列里put元素，队列会一直阻塞生产者线程直到put数据or相应中断退出 |
|          | 当阻塞队列空时，消费者继续往队列里take元素，队列会一直阻塞消费者线程直到队列可用 |
| 超时退出 | 当阻塞队列满时，队列会阻塞生产者线程一定时间，超过限时后生产者线程会退出 |
|          | 当阻塞队列空时，队列会阻塞消费者线程一定时间，超过限时后消费者线程会退出 |

------

### ThreadPool线程池

例子：

10年前单核CPU电脑，假的多线程，像马戏团小丑玩多线程，CPU需要来回切换。

现在是多核电脑，多个线程各自在独立的CPU上，不用切换，效率高。



**线程池的优势**

线程池做的工作主要是控制运行的线程数量，**处理过程中将任务放入队列**，然后线程创建后启动这些任务，**如果线程数量超过了最大数量，超出的线程排队等待**，等其它线程执行完毕，再从队列中取出任务来执行。



主要特点：**线程复用；控制最大并发数；管理线程**。

1. 降低资源消耗

   通过重复利用已创建的线程**降低线程创建和销毁造成的消耗**。

2. 提高响应速度

   当任务到达，任务可以**不需要等待线程创建就立即执行**。

3. 提高线程的可管理性

   线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以**进行资源统一的分配，调优和监控**。

------



```java
import java.util.concurrent.Executor;  // JUC包下   Executor是接口
import java.util.concurrent.ExecutorService; //Executor的子接口
import java.util.concurrent.Executors; //线程池的工具类
```

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go3iolowpkj31980s2qri.jpg" alt="WeChat0983953fee3b34bf5c560df60ab90e6a.png" style="zoom:50%;" />

**线程池的核心就是ThreadPoolExecutor!**

------

#### 线程池的参数

```java
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

**7大参数**

- corePoolSize

  **线程池中的常驻核心线程数**。

- maximumPoolSize

  **线程池中能容纳同时执行的最大线程数**，此值必须大于等于1。

- keepAliveTime

  **多余的空闲线程的存活时间**。

  当前线程池中线程数量超过corePoolSize时，当空闲时间达到keepAliveTime时，多余线程会被销毁直到只剩下corePoolSize个线程为止。

- unit

  **keepAliveTime的单位**。

- workQueue

  **任务队列**，被提交但未被执行的任务。

- threadFactory

  表示**生成线程池中工作线程的线程工厂**，用于创建线程，一般**默认**的即可。

- handler

  **拒绝策略**，表示当**队列满了，并且工作线程大于等于线程池的最大线程数(maximumPoolSize)**时，**如何来拒绝请求执行的runnable策略**。

------

#### JDK的内置拒绝策略

ThreadPoolExecutor中有四种拒绝策略,这四种策略都实现了**RejectedExecutionHandler**接口

- AbortPolicy (默认)

  直接抛出RejectedExecutionException异常阻止系统正常运行

- CallerRunsPolicy

  “调用者运行”一种调节机制，该策略既不会抛弃任务，也不会抛出异常，而是将某些任务回退到调用者，从而降低新任务的流量。

- DiscardOldestPolicy

  抛弃队列中等待最久的任务，然后把当前任务加入队列中尝试再次提交当前任务。

- DiscardPolicy

  该策略默默地丢弃无法处理的任务，不予任何处理也不抛出异常。

  如果允许任务丢失，这是最好的一种策略。

------

#### 线程池底层原理

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go3mz13ckwj31480s2b29.jpg" alt="WeChatf7097dfb52c254487876997fc94682da.png" style="zoom:33%;" />

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go3mvtxgo8j31bi18y1kx.jpg" alt="WeChatba8979183a3215c18dadcf777600a30a.png" style="zoom:33%;" />

**流程**

1. 在创建线程池后，开始等待请求。

2. 当调用execute()方法添加一个请求任务时，线程池会做出如下判断：

   1. 如果正在运行的线程数量小于corePoolSize，那么马上创建线程运行这个任务；
   2. 如果正在运行的线程数量大于等于corePoolSize,那么将这个任务**放入队列**；
   3. 如果这个时候队列满了且正在运行的线程数量还小于maximumPoolSize,那么还是要创建非核心线程立刻运行这个任务;
   4. 如果队列满了且正在运行的线程数量等于maximumPoolSize,那么线程池会启动**饱和拒绝策略**来执行。

3. 当一个线程完成任务时，它会从队列中取下一个任务来执行。

4. 当一个线程无事可做超出一定的时间(KeepAliveTime)时，线程会判断：

   如果当前运行的线程数大于corePoolSize,那么这个线程就被停掉。

   所有线程池的任务完成后，它最终会**收缩到corePoolSize的大小**。





------



#### **3种不同参数的ThreadPoolExecutor线程池**

- Executors.newFixedThreadPool(int n)

  一池固定n个工作线程。

- Executors.newSingleThreadExecutor;

  一池一个工作线程

- Executors.newCachedThreadPool()

  动态控制一池的线程数,可伸缩扩容，根据并发量控制线程数量





<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go3jwmrcchj31fw0p8e82.jpg" alt="WeChatd41120430f34ee4a85f650c943340b0e.png" style="zoom:50%;" />



**newFixedThreadPool**

```java
// 源码
public static ExecutorService newFixedThreadPool(int nThreads) {
  return new ThreadPoolExecutor(nThreads, nThreads,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>());
}
```

```java
/**
pool-1-thread-1	办理业务
pool-1-thread-3	办理业务
pool-1-thread-4	办理业务
pool-1-thread-2	办理业务
pool-1-thread-5	办理业务
pool-1-thread-1	办理业务
pool-1-thread-3	办理业务
pool-1-thread-3	办理业务
pool-1-thread-2	办理业务
pool-1-thread-4	办理业务
**/
void newSingleThreadExecutorDemo(){
        // 一池1个受理线程，类似一个银行有1个受理窗口
        ExecutorService threadPool = Executors.newSingleThreadExecutor();
        try {
            // 模拟10个顾客来办理业务，目前有1个工作人员在窗口提供服务
            for(int i=0;i<10;i++){
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"\t办理业务");
                    try {
                        Thread.sleep(new Random().nextInt(5)*1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
}
```

**newSingleThreadExecutor**

```java
// 源码
public static ExecutorService newSingleThreadExecutor() {
  return new FinalizableDelegatedExecutorService
    (new ThreadPoolExecutor(1, 1,
                            0L, TimeUnit.MILLISECONDS,
                            new LinkedBlockingQueue<Runnable>()));
}
```

```java
/**
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
pool-1-thread-1	办理业务
**/
void newSingleThreadExecutorDemo(){
        // 一池1个受理线程，类似一个银行有1个受理窗口
        ExecutorService threadPool = Executors.newSingleThreadExecutor();
        try {
            // 模拟10个顾客来办理业务，目前有1个工作人员在窗口提供服务
            for(int i=0;i<10;i++){
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"\t办理业务");
                    try {
                        Thread.sleep(new Random().nextInt(5)*1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
}
```

**newCachedThreadPool**

```java
// 源码
public static ExecutorService newCachedThreadPool() {
  return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                60L, TimeUnit.SECONDS,
                                new SynchronousQueue<Runnable>());
}
```

```java
/**
pool-1-thread-1	办理业务
pool-1-thread-3	办理业务
pool-1-thread-4	办理业务
pool-1-thread-2	办理业务
pool-1-thread-5	办理业务
pool-1-thread-6	办理业务
pool-1-thread-7	办理业务
pool-1-thread-8	办理业务
pool-1-thread-9	办理业务
pool-1-thread-10	办理业务
**/
void newCachedThreadPoolDemo(){
        // 一池n个受理线程，类似一个银行有n个受理窗口
        ExecutorService threadPool = Executors.newCachedThreadPool();
        try {
            // 模拟10个顾客来办理业务，目前有n=10个工作人员在窗口提供服务
            for(int i=0;i<10;i++){
                threadPool.execute(()->{
                    System.out.println(Thread.currentThread().getName()+"\t办理业务");
                    try {
                        Thread.sleep(new Random().nextInt(5)*1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                });
            }
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            threadPool.shutdown();
        }
 }
```

------

**面试题**：**在工作中单一的/固定数的/可变的三种创建线程池的方法哪个用得多？**

**一个都不用,工作中只能使用自定义**。

阿里巴巴Java开发手册给出： 

线程池不允许用Executors去创建，而是通过ThreadPoolExecutor的方式，这样的处理方式让写的同学更加明确线程池的运行规则，避免资源耗尽的风险。

说明：Executors返回的线程池对象的弊端如下：

1. **FixedThreadPool**和**SingleThreadPool**:

   允许的请求队列长度为Integer.MAX_VALUE，可能会堆积大量的请求，从而导致OOM。

2. **CachedThreadPool**和**ScheduledThreadPool**：

   允许的创建线程数量为Integer.MAX_VALUE, 可能会创建大量的进程，从而导致OOM。

------

**面试题：如何确定maximumPoolSize大小？**

- CPU密集型的计算机中

```java
maximumPoolSize = Runtime.getRuntime().availableProcessors()+1;
```

- IO密集型的计算机中

```
maximumPoolSize = Runtime.getRuntime().availableProcessors()/阻塞系数;
```

------

