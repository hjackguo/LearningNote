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

- BIO：Block IO**同步阻赛式**IO，就是我们平时使用的传统IO，它的特点是模式**简单**使用方便，**并发处理能力低**。
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

- Collection是一个集合接口，它提供了对集合对象