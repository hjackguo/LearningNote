# 内存与垃圾回收篇

## JVM与JAVA体系结构

JAVA文件  -> .class文件  -> JVM上运行



### JVM整体结构

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnr83odclbj30ma0kyqn0.jpg" alt="WeChat4ffd409c2ad390b0c712beb1f847c624.png" style="zoom: 50%;" />



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnr85ry6o3j31gs13u7wi.jpg" alt="WeChat543362e2c9c03cfe02a31d02d3cf5a4e.png" style="zoom:50%;" />



**多线程**共享： **方法区、堆**

**单线程**独占：**虚拟机栈、本地方法栈、程序计数器**



### JAVA代码执行流程



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnr8g1fecoj311u0rmnf4.jpg" alt="WeChat768ef409d70cccd5c842073e6e4120a7.png" style="zoom:50%;" />



### class文件

```java
public static void main(String[] args) {
  int i = 2;
  int j = 3;
  int k = i + j;
}

// 反汇编器  可以查看java编译器为我们生成的字节码
javap -v App.class
  
 public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: iconst_2   // 变量2
         1: istore_1
         2: iconst_3   // 变量3
         3: istore_2
         4: iload_1
         5: iload_2
         6: iadd       // 加法
         7: istore_3
         8: return
```



### JVM生命周期



**虚拟机的启动**

Java虚拟机的启动时通过**引导类加载器(bootstrap class loader)**创建一个**初始类(initial class)**来完成的，这个类是由虚拟机的具体实现制定的。



**虚拟机的执行**

- 一个运行中的Java虚拟机任务是：执行Java程序
- 程序开始执行时虚拟机才运行，程序结束时虚拟机就停止
- 执行一个Java程序的时候，在执行的是一个**Java虚拟机的进程**



**虚拟机的退出**

- 程序正常执行结束
- 程序执行过程遇到异常而异常终止
- 由于操作系统出现错误而导致Java虚拟机进程终止
- 某线程调用Runtime类或System类的exit方法



## 类加载子系统

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnrbr3k9uzj312g0di7kd.jpg" alt="WeChatdebb2e2c64a5e020ffd961fcf67af293.png" style="zoom:50%;" />

 

### 类加载子系统作用

- 类加载器子系统负责从文件**加载Class文件**
- **ClassLoader**只负责**class文件的加载**，至于它是否可以**运行**，则**由Execution Engine**决定
- 加载的类信息存放于一块为方法区的内存空间。除了**类的信息**外，**方法区**中还会存放**运行时常量池信息**。



### 类加载器ClassLoader角色

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnrc919gj1j30uc0omk9x.jpg" alt="WeChat0ed09f52445792a75de71c8b58a55aa4.png" style="zoom:50%;" />



### 类的加载过程

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnrcchhds0j30q80kc48r.jpg" alt="WeChat10014a501b38252544d1d2af25a55d0c.png" style="zoom:50%;" />

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnrcdo6pvoj30wg0aw1kx.jpg" alt="WeChat484ff34ae590e312d431c559319e0e04.png" style="zoom:50%;" />



**加载**

- 通过一个类的全限定名获取定义此类的二进制字节流
- 将这个字节流所代表的静态存储结构转化为方法区的运行时数据结构
- **在内存中生成一个代表这个类的java.lang.Class对象**，作为方法区这个类的各种数据访问入口。



**链接**

- 验证

  目的在于确保Class文件的字节流中包含信息符合当前虚拟机要求，保证被加载类的正确性

- 准备

  为类变量分配内存并且设置该类变量的默认初始值，即零值

- 解析

  将常量池内的符号引用转换为直接引用的过程



**初始化**

- 初始化阶段就是执行**类构造器方法<clinit>()**的过程。
- javac编译器自动收集类中所有类变量的赋值动作和静态代码块中的语句合并而来。
- 若该类具有父类，JVM会保证子类<clinit>执行前，父类的<clinit>()已经执行完毕。
- 虚拟机必须保证一个类的<clinit>()方法在多线程下被同步加锁。





### 类加载器的分类

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnrfqpvd90j30ys0jewvt.jpg" alt="WeChat5d4f777369863c7a9a34627f63ecdf4c.png" style="zoom:33%;" />

```java
public class ClassLoaderTest {
    public static void main(String[] args) {
        // 获取系统类加载器
        ClassLoader systemClassLoader = ClassLoader.getSystemClassLoader();
        // jdk.internal.loader.ClassLoaders$AppClassLoader@73d16e93
        System.out.println(systemClassLoader);

        //获取其上层：扩展类加载器
        ClassLoader extClassLoader = systemClassLoader.getParent();
        // jdk.internal.loader.ClassLoaders$PlatformClassLoader@85ede7b
        System.out.println(extClassLoader);

        // 获取其上层
        ClassLoader bootstrapClassLoader = extClassLoader.getParent();
        // null 获取不到引导类加载器, bootstrap c++编写，不允许拿
        System.out.println(bootstrapClassLoader);

        // 用户自定义类类来说: 默认使用系统类加载器来加载
        ClassLoader classLoader = ClassLoaderTest.class.getClassLoader();
        // jdk.internal.loader.ClassLoaders$AppClassLoader@73d16e93
        System.out.println(classLoader);

        // String类使用引导类加载器加载 --> Java核心类库都是
        ClassLoader stringClassLoader = String.class.getClassLoader();
        System.out.println(stringClassLoader);    // null
    }
}
```



**启动类加载器**

引导类加载器，Bootstrap ClassLoader

C/C++实现，嵌套在JVM内部

用来加载Java的核心库

并不继承自java.land.ClassLoader，没有父加载器

加载扩展类和应用程序类加载器，并指定为他们的父类加载器。

出于安全考虑，Bootstrap启动类加载器只加载包名为java, javax, sun 等开头的类



**扩展类加载器**

Java编写，由 sun.misc.Launcher$ExtClassLoader实现

派生于ClassLoader类

父类加载器为启动类加载器



**应用程序类加载器**

系统类加载器，AppClassLoader

Java编写，由 sun.misc.Launcher$AppClassLoader实现

派生于ClassLoader类

父类加载器为扩展类加载器

该类是程序中默认的类加载器，Java应用的类都是由他来完成

通过ClassLoader.getSystemClassLoader()方法可以获取到该类加载器



**用户自定义类加载器**

日常开发，类的加载几乎是上述3种类加载器相互配合执行。必要时，我们需要自定义类加载器，来定制类的加载方式



自定义类加载器作用

- 隔离加载类
- 修改类的加载方式
- 扩展加载源
- 防止源码泄漏



继承java.lang.ClassLoader



**ClassLoader类**

ClassLoader类是一个抽象类，其后所有的类加载器都继承自ClassLoader(不包括引导类加载器)

所以，JVM支持两种类型的类加载器，分别是引导类加载器和自定义类加载器。



### 双亲委派机制(*)

Java虚拟机对class文件采用**按需加载**的方式。而且加载某个类的class文件时，Java虚拟机采用的是**双亲委派模式**，即把请求交由父类处理，它是一种任务委派模式。



**双亲委派机制工作原理**

1. 如果一个类加载器收到了类加载请求，它不会自己去加载，而是把这个请求委托给父加载器去执行
2. 如果父亲加载器还存在父亲加载器，则进一步向上委托，直到到达顶层的启动类加载器；
3. 如果父类加载器可以完成类加载任务，就成功返回，如果不行，自加载器才会去尝试自己加载。



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gns55jm863j30sc0nahdt.jpg" alt="WeChat9db08550f4a25a79051be24a1aba828b.png" style="zoom:33%;" />



**双亲委派机制优势**

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gns5docnnkj30r20jiqv5.jpg" alt="WeChatec07934fe51a294ea97211b2f1442f78.png" style="zoom:33%;" />

- 避免类的重复加载

- 保护程序**安全**，防止核心API被随意篡改

  如攻击者自定义类: java.lang.String

  偷偷把数据窃听



**沙箱安全机制**

自定义String类，但在加载自定义String类会先使用引导类加载器加载，而引导类加载器会先加载jdk自带文件里的String.class，报错信息说没有main方法，就是因为加载的是rt.jar包里的String类。这样可以保证对java核心源代码的保护，这就是**沙箱安全机制**。



### 类的主动使用与被动使用



在JVM中判断两个class对象是否为同一个类存在两个必要条件：

- 类的完整类名必须一致，包括包名
- 加载这个类的ClassLoader(指ClassLoader实例对象)必须相同 



**对类加载器的引用**

JVM必须知道一个类型是由启动加载器加载还是用户类加载器加载的。如果一个类型是由用户类加载器加载的，那么JVM会将这个**类加载器**的一个**引用**作为类型信息的一部分保存在**方法区**中。



**类的主动使用**

- 创建类的实例
- 访问某个类或接口的静态变量，或对其赋值
- 调用类的静态方法
- 反射
- 初始化一个类的子类
- Java虚拟机启动时被标注明为启动类的类



其余为类的**类的被动使用**，都**不会导致类的初始化**(initialization)。



## 运行时数据区概述及线程

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnsjtfinntj30z40cuqhk.jpg" alt="WeChatb4929b631d3a69ba21945d96dd89fe67.png" style="zoom:50%;" />



JVM规定了JAVA运行过程中内存的申请、分配、管理。



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnsk1e0e1nj316c0j6x6q.jpg" alt="WeChat91b80d860820d8da4d53a094eb8e8c5d.png" style="zoom:50%;" />



每个线程独有：程序计数器、虚拟机栈、本地方法栈

线程间共享：堆、(堆外内存(永生代或元空间、代码缓存)) 方法区

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnsk7w4qojj30l20kwtlp.jpg" alt="WeChatc13b1b82929c6281cdc3e8b5fad955ed.png" style="zoom:50%;" />



**线程**

- 一个JVM实例对应一个Runtime实例， **Runtime相当于运行时数据区**。

  当一个Java线程准备好执行以后，此时一个操作系统的本地本地线程也同时创建。Java线程执行以后，本地线程也会回收。

- 操作系统负责所有线程的安排调度到任何一个可用的CPU上。一旦本地线程初始化成功，它就会调用Java线程中的run()方法。



**线程**

Hotspot JVM里，每个线程都与操作系统的本地线程直接映射(一对一模型)。

线程分为**守护线程**、**普通线程**



后台有许多线程在运行，Hotspot JVM里面主要有：

- 虚拟机线程

  需要JVM达到安全点才回出现。

- 周期任务线程

  这种线程是时间周期事件的体现(比如：中断)

- GC线程

  为JVM中垃圾收集提供支持

- 编译线程

  这种进程在运行时会将字节码编译成本地代码

- 信号调度线程

  这种线程接收信号并发送给JVM，在它内部通过调用适当的方法进行处理。



------

