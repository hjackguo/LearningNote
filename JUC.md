### **并发类**

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



### synchronized

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



### **Lock**

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

