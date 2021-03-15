## 单例模式

### 饿汉式

> 优点

写法简单，类装载时完成实例化。**避免了多线程同步问题**。

> 缺点

在类装载的时候就完成了实例化，没有达到Lazy Loading的效果。如果从开始到结束没用这个实例，**可能造成内存浪费**。



#### 静态常量(*)

```java
class Singleton{
    // 1.构造器私有化
    private Singleton(){}

    // 2.本类内部创建对象实例
    private final static Singleton instance = new Singleton();

    // 3.提供一个公有的静态方法，返回实例对象
    public static Singleton getInstance(){
        return instance;
    }
}
```



#### 静态代码块(*)

```java
class Singleton{
    // 1.构造器私有化
    private Singleton(){}

    // 2.本类内部创建对象实例
    private static Singleton instance;
    
    static {    // 静态代码块中创建单例对象
        instance = new Singleton();
    }

    // 3.提供一个公有的静态方法，返回实例对象
    public static Singleton getInstance(){
        return instance;
    }
}
```

### 懒汉式

#### 线程不安全

```java
class Singleton{
    private static Singleton instance;
    // 1.构造器私有化
    private Singleton(){}

    // 2.提供一个公有的静态方法，当使用到该方法才去创建
    // 即懒汉式
    public static Singleton getInstance(){
        if(instance==null) instance = new Singleton();
        return instance;
    }
}
```

> 优点

起到了lazy Loading的效果

> 缺点

只能在**单线程**下使用

> 结论

实际开发中，不要使用这种方式。



#### 线程安全，同步方法

```java
class Singleton{
    private static Singleton instance;
    // 1.构造器私有化
    private Singleton(){}

    // 2.提供一个公有的静态方法，当使用到该方法才去创建。加入同步处理，解决线程安全问题
    synchronized public static Singleton getInstance(){
        if(instance==null) instance = new Singleton();
        return instance;
    }
}
```

> 优点

加了synchronized，**解决了线程不安全问题**

> 缺点

**效率太低**了，每个线程想获得类的实例时，执行getInstance()方法都要进行同步。而其实这个方法只执行一次实例化代码就够了，后面想要直接return。

> 结论

实例开发中，不推荐使用。



### 双重检查(*)

```java
class Singleton{
    // 注意，本处的volatile关键字实现线程中的可见性，避免一个线程创建了后，另一个线程的工作内存还是null的情况
    private static volatile Singleton instance;
    // 1.构造器私有化
    private Singleton(){}

    // 2.提供一个公有的静态方法，加入双重检查代码，解决线程安全问题，同时解决懒加载问题。
    public static Singleton getInstance(){
        if(instance==null){
            synchronized (Singleton.class){
                // 本处双重验证！！！
                if(instance==null) instance = new Singleton();
            }
        }
        return instance;
    }
}
```

> 优点

Double- Check 是多线程开发下经常使用到的。线程安全；延迟加载；效率高

> 结论

推荐使用。



### 静态内部类(*)

```java
class Singleton{
    private static Singleton instance;
    // 构造器私有化
    private Singleton(){}
		// 返回静态内部类的静态属性
    public static Singleton getInstance(){
        return SingletonInstance.INSTANCE;
    }
  	// 静态内部类，类中有静态属性
    public static class SingletonInstance{
        private static final Singleton INSTANCE = new Singleton();
    }
}
```

> 原理

静态内部类方式在Singleton类被装载时并不会立即实例化，而是在需要实例时，调用getInstance方法，才会装载SingletonInstance类。

> 优点

采用了类装载机制来保证初始化实例时只有一个线程。

类的静态属性只会在第一次加载类的时候初始化，JVM保证了线程的安全性，在类进行初始化时，别的线程是无法进入的。

**避免了线程不安全**，**利用静态内部类特点实现延迟加载**。

> 结论

推荐使用。

### 枚举(*)

```java
// 使用枚举可以实现单例，推荐使用
enum  Singleton{
    INSTANCE; // 属性
    public void sayOK(){
        System.out.println("OK");
    }
}
```

> 优点

用枚举实现单例模式。不仅能避免多线程同步问题，而且能防止反序列化重新创建新的对象。

> 结论

推荐使用。



## 工厂模式

![WeChat27c1129664dc58e229d83b5e57f364bc.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gok0wpc96kj30s40fm14z.jpg)

没有用工厂模式的情况下，新增一个PepperPizza, 所有的OrderPizza都要进行修改。 

> 意义

将实例化对象的代码抽取出来，放到一个类中统一管理和维护，达到和主项目的**依赖解藕**。从而提高项目的扩展性和维护性。依赖抽象原则。

- 创建对象时，不要直接new，而是把new动作放到一个工厂方法里，并放回。
- 不要让类继承具体类，而是继承抽象类或者是实现interface
- 不要覆盖基类中已经实现的方法。



### 简单工厂模式

**简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例**。

简单工厂模式也叫**静态工厂模式(Static)**

定义了一个创建对象的类，由这个类来封装实例化对象的行为。

![WeChat4a7799703bb6582db27791d8cf6f5918.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gok2w2uqp3j30rw0mik3m.jpg)

使用简单工厂后，增加Pizza时，只需要修改SimpleFactory中的代码

```java
public class SimpleFactory {
    // 根据OrderType返回对应Pizza对象
    public static Pizza createPizza(String orderType){
        System.out.println("使用简单工厂模式");

        Pizza pizza = null;
        if(orderType.equals("greek")){
            pizza = new GreekPizza();
        }else if(orderType.equals("cheese")){
            pizza = new CheesePizza();
        }
        pizza.setName(orderType);
        return pizza;
    }
}
```

### 工厂方法模式

定义一个创建对象的**抽象方法**，由子类决定要实例化的类。**工厂方法模式将对象的实例化推迟到子类**。

![WeChat77deee86d5c6726415b5a4d95ba73b7a.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gok6l2398dj30tm0o0app.jpg)

### 抽象工厂模式

定义了一个interface用于创建相关或有依赖关系的对象簇，而无需指明具体的类。

![WeChatceb0dbc3251b12de7029592a0639c9a2.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gok6vul33fj314y0v0qu1.jpg)

### 几种方式对比

> 传统方式

1. 优点是好理解，操作简单
2. 缺点是违反了OCP原则，即**对扩展开放，对修改关闭**。即当我们给类增加新功能的时候，尽量不修改代码，或者尽可能少修改代码。

> 改进思路

分析： 修改代码可以接受，但是如果我们在其它地方也有创建Pizza的代码，就意味着，也需要修改。而创建Pizza的代码往往有多处。

思路：**把创建Pizza对象的封装到一个类中**，这样我们有新的Pizza种类时，只需要修改该类就可以了，其它有创建到Pizza对象的代码就不需要修改了——**简单工厂模式**

> 简单工厂模式

定义了一个创建对象的类，由这个类来封装实例化对象的行为。

> 工厂方法模式

定义抽象函数，把对象的实例化推迟到工厂子类

> 抽象工厂模式

定义了一个**interface**, 将工厂抽象成两层，一层是**AbsFactory(抽象工厂)和具体的工厂子类**。

