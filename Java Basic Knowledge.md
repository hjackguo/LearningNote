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

![1414839-20200411170135520-938948148](/Users/guohuanjie/Documents/programming/LearnNote/image/1414839-20200411170135520-938948148.png)

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
- 性能：Array List在性能方面优于Vector。
- 扩容：ArrayList和Vector都会根据实际的需要动态调整容量，只不过在**Vector**扩容每次会增加**1倍**，而**ArrayList**只会增加**50%**。

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

