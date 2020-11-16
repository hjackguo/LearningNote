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

