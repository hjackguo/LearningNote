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

### 程序计数器(PC寄存器)

Program Counter Register, 也有称为程序钩子，**JVM中的PC寄存器时对物理PC寄存器的一种抽象模拟**。

PC寄存器用来**存储指向下一条指令的地址**，也即将要执行的指令代码。 **由执行引擎读取**下一条指令。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnt224fulrj30ki0h84ah.jpg" alt="WeChat4afc20e5ae60771d9be2c78a0caf54da.png" style="zoom:50%;" />

每个线程都有自己的程序计数器，是**线程私有**的，生命周期与线程生命周期保持一致。

任何时间一个线程都只有一个方法在执行，也就是所谓的**当前方法**。程序计数器会存储当前线程正在执行的Java方法的JVM指令地址。如果执行的是native方法，则是未指定值(undefined)。

```java
public static void main(String[] args) {
  int i = 10;
  int j = 20;
  int k = i+j;

  String s = "abc";
  System.out.println(i);
  System.out.println(k);
}
```

上述代码生成.class文件后

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnt2q2f9kgj30lw0f614k.jpg" alt="WeChat830cec095d38459c223825a771558acb.png" style="zoom:50%;" />

红色边框里的编号便是**存放在PC计数器里的指令地址(偏移地址)**



**使用PC寄存器存储字节码指令地址有什么用？**

因为CPU需要不停的切换各个线程，这时候切换回来后，就得知道接着从哪开始继续执行。



**PC寄存器为什么会被设定为线程私有？**

为了能够准确记录各个线程正在执行的当前字节码指令地址，最好的办法是为一个线程都分配一个PC寄存器。



### 虚拟机栈

栈是运行时的单位，堆是存储的单位

每个线程在创建时都会创建一个虚拟机栈，内部保存一个个的栈帧(Stack Frame)，对应着一次次的Java方法调用。 

栈是线程私有的。

主管Java程序的运行，它保存方法的局部变量(8种基本数据类型、对象的引用地址)、部分结果，并参与方法的调用和返回。

栈存在OOM



**栈中可能出现的异常**

Java虚拟机规范允许**Java栈的大小是动态的或者是固定不变的**。

- 如果采用**固定大小**的Java虚拟机栈，如果线程请求分配的栈容量超过Java虚拟机栈允许的最大容量，Java虚拟机将会抛出一个**StackOverflowError** 异常。
- 如果Java虚拟机栈可以**动态扩展**，并且尝试扩展时无法申请到足够的内存，那么线程没有足够的内存去创建对应虚拟机栈，Java虚拟机会抛出一个**OutOfMemoryError**异常。



**设置栈内存大小**

参数 **-Xss** 设置线程的最大栈内存空间，栈的大小直接决定了函数调用的最大可达深度



**栈中存储什么**

- 每个线程都有自己的栈，栈中的数据都是以**栈帧(Stack Frame)**的格式存在。
- 在这个线程上正在执行的每个方法都有各自对应的一个栈帧
- 栈帧时一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息。



Java方法有两种返回函数的方式,一种是正常的函数返回，使用return指令；另外一种是抛出异常。不管哪种方式，都会**导致栈帧被弹出**。



#### 栈帧

**栈帧的内部结构**

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnt7fhqubnj31880gewuh.jpg" alt="WeChat9036274a67a4c6be23a6b8b4e27fe8e8.png" style="zoom:50%;" />

每个栈帧中存储着：

- **局部变量表**(Local Variables)
- **操作数栈**(Operand Stack) (或表达式栈)
- 动态链接(Dynamic Linking) (或指向运行时常量池的方法引用)
- 方法返回地址(Return Address) (或方法正常退出或者异常退出的定义)
- 一些附加信息



##### 局部变量表

- 局部变量表也称为局部变量数值或本地变量表
- 定义为一个**数字数组，用于存储方法参数和定义在方法体内的局部变量**，这些数据类型包括各类基本数据类型、对象引用(reference),以及returnAddress类型。
- **局部变量表所需的容量大小是在编译期确定下来的**。在方法运行期间不会改变局部变量表的大小。



<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnta8190fxj3114080dij.jpg" alt="WeChatb7de78ffa371481d85fdd7d351b1520e.png" style="zoom:50%;" />

Start PC  是指字节码文件中作用域从第几行开始

Length 作用域的长度

Start PC + Length在此都相同，因为函数结束，局部变量全部失效



局部变量表，最基本的存储单元是**Slot(变量槽)**

局部变量表里，32位以内的类型只占一个slot(包括returnAddress类型,引用)，64位的类型(long和double)占用两个slot

JVM会为局部变量表中的每一个slot都分配一个访问索引，通过这个索引即可成功访问到局部变量表中指定的局部变量值。

当一个实例方法被调用的时候，它的方法参数和方法体内部定义的局部变量将会**按照顺序**被复制到局部变量表中的每一个slot上。

如果需要**访问局部变量表中一个64bit的局部变量值，只需要访问第一个索引即可**

如果当前帧是由构造方法或者实例方法创建时，那么**对象引用this将会存放在index为0的slot处**。**static方法没有this**。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gntbflnn19j30dk0fg79k.jpg" alt="WeChat55d3cd556742ab03b51b421c379b77f1.png" style="zoom: 50%;" />

```java 
    // slot重复利用
    void test4(){
        int a = 0;
        {
            int b = 0;
            b = a + 1; // b的 作用域起点+作用域长度< a,c
        }
        int c = a+1; // c的slot是b原来的
    }

      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            4       4     2     b   I
            0      13     0  this   Lcom/company/jvm/stack/StackTest;
            2      11     1     a   I
           12       1     2     c   I
/**
变量的分类： 按照数据类型分： 基本数据类型   引用数据类型
				 按照在类中声明分： 成员变量(类变量、实例变量)   局部变量
**/

```

**变量的分类**

- 按数据类型分类

  - 基本数据类型
  - 引用数据类型

- 按在类中声明的位置分

  - 成员变量

    在使用前，都经历过默认初始化赋值

    - 类变量 

        linking的 prepare阶段：给类变量赋值  

      ​                ---> initial阶段：给类变量显示赋值及静态代码块赋值

    - 实例变量

       随着对象的创建在堆空间中分配实例变量空间，并进行赋值

  - 局部变量

    在使用前，必须要进行显示赋值！否则编译不通过



**补充说明**

在栈帧中，与性能调优关系最为密切的部分就是局部变量表。

局部变量表中的变量也是重要的垃圾回收根节点，只要**被局部变量表中直接或间接引用的对象都不会被回收**。



##### 操作数栈

Operand Stack，也称为表达式栈

操作数栈在方法执行过程中，根据字节码指令，往栈中写入数据或者提取数据。

- 某些字节码指令将值压入操作数栈，其余的字节码指令将操作数取出栈。使用它们后再把结果压入栈。
- 比如：执行复制、交换、求和操作等
- **如果被调用的方法带有返回值，其返回值将会被压入当前栈帧的操作数栈中**，并更新PC寄存器中下一条需要执行的字节码指令。
- Java虚拟机的**解释引擎是基于栈的执行引擎**，其中的栈指的就是**操作数栈**。
- 操作数栈，**主要用于保存计算过程中间结果**，**同时作为计算过程中变量临时的存储空间**。
- 操作数栈就是JVM引擎的一个工作区，当一个方法刚开始执行的时候，一个新的栈帧就会被创建，**这个方法的操作数栈也是空的**。
- 每一个操作数栈都会有一个明确的栈深度用于存储数值，其所需的最大深度在编译期就定义好了，保存在方法的Code属性中，为max_stack的值。
- 栈中的任何一个元素都可以是Java任意数据类型。
  - 32bit的类型占用一个栈单位深度
  - 64bit的类型占用两个栈单位深度
- 操作数栈**并非采用访问索引方式来进行数据访问的**，而是通过push和pop操作完成数据访问。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gntlsabvr0j30xc09wn6v.jpg" alt="WeChatf7cec22f9f7183c4d6299d79e7e63690.png" style="zoom:33%;" />



**栈顶缓存技术**

Top-of-Stack-Cashing

由于操作数是存储在内存中，频繁地执行内存读/写会影响执行速度。因此Hotspot JVM提出了栈顶缓存技术。**将栈顶元素全部缓存在物理CPU的寄存器中，以此降低对内存的读/写次数，提升执行引擎的执行效率**。



##### 动态链接

Dynamic Linking



