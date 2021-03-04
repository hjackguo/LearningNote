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

什么时候会使用：反射的方式。 反射的特征：动态性。

> 疑问2：反射机制与面向对象中的封装性是不是矛盾的？如何看待两个技术？

 不矛盾。



