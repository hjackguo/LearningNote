## Redis数据类型

- String

- List

- Set

- Hash

- Zset

  有序集合。



### String类型(SDS)

Redis中的String是可以修改的，称为**动态字符串(Simple Dynamic String, 简称SDS)**。内部结构像一个ArrayList,**维护着一个字节数组**。

> Redis内存分配机制如下

- **当字符串长度小于1MB时，每次扩容都是加倍现有空间**。
- **当字符串长度大于1MB时，每次扩容只会扩张1MB的空间**。

```c++
struct SDS{
  T capacity;       //数组容量
  T len;            //实际长度
  byte flages;  //标志位,低三位表示类型
  byte[] content;   //数组内容
}
```



### ZSET类型(跳表)

zset是**有序**的set

zset底层是**跳表**实现，跳表能让链表实现**log(n)查找**。空间换时间。

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go7jw7aubij311c0b47lf.jpg" alt="WeChatefe4637190b4888507ad8fba188e6c70.png" style="zoom:50%;" />

> 跳表如何实现谁来进行提拔成上层索引？

**抛硬币**，途中新插入了95，它有50%概率被提拔到最底层索引，有25%概率被提拔到中间层索引，有12.5%概率被提拔到顶层索引。

> 为什么要用抛硬币这种奇怪的方式？

因为**跳跃表删除和添加节点是不可预测**的，很难用一种有效算法来保证跳表的索引部分始终均衡。抛硬币的方法不能保证索引绝对均匀分布，却可以大体趋向均匀分布。

> 跳表插入流程

1. 新节点和各层索引结点逐层比较，确定原链表插入位置O(log N)
2. 把索引插入到原链表。O(1)
3. 利用抛硬币的随机方式，决定新节点是否提升为上一级索引，结果为”正“则提升并继续抛硬币，结果为“负“则停止。O(logN)

总体上，**跳跃表插入操作的时间复杂度是O(log N)**,而这种结构占用空间是2N，即空间复杂度是O(N)

> 跳跃表的删除

删除操作比较简单，只要在索引层找到要删除的节点，然后**顺藤摸瓜**，**删除每一层的相同节点**即可。

如果某一层索引删除**后只剩下一个节点了，那么一层就可以干掉了**。

如果要删除节点值为5，删除前：

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go7kdmkfinj31320e6duf.jpg" alt="WeChat58fbe098017d20c356641365bb53d3e8.png" style="zoom:50%;" />

删除后：

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1go7ke41i88j311g094all.jpg" alt="WeChate88c62ddbc221ebfb99c1aa902197beb.png" style="zoom:50%;" />

具体步骤：

1. 自上而下，查询第一次出现节点的索引，并逐层找到每一层对应的节点。O(log N)
2. 删除每一层查找到的节点，如果该层只剩下1个节点，删除整个一层(原链表除外)。O(log N)

总体上，跳跃表删除时间复杂度O (log N)

> 跳跃表和二叉查找树的区别是什么？

**跳跃表的优点是维持结构平衡的成本比较低**，完全**依靠随机**。而**二叉树**在多次插入删除后，**需要Rebalance**来重新调整结构平衡。

**跳跃表的缺点是，需要存储索引点，比起二叉树更占用内存。**跳表的空间复杂度是O(N), 二叉树是O(1)。



## Redis为什么快？

1. 完全基于内存操作
2. C语言实现，优化过的数据结构。
3. 使用单线程，无上下文的切换成本
4. 基于非阻塞IO多路复用机制。



## Redis持久化

**RDB持久化(默认)**

- RDB (Redis Databse)是系统**默认**的持久化方式
- 通过**快照**实现，Redis自动将内存中的数据进行快照并**持久化到硬盘**。
- Redis会在**指定的情况**下触发快照，如多少时间内多少个key被修改。

**AOF持久化**

- **默认**情况下Redis**没有开启**AOF (Append only file)方式的持久化
- 开启AOF持久化后，**每执行一条Redis中的数据命令，Redis就会将该命令写入硬盘的AOF文件**，这一过程会**降低Redis性能**，但大部分情况下可接受。



## 缓存处理流程

<img src="http://ww1.sinaimg.cn/large/008aPpVGgy1gnvk0t6t2nj30vw0ty7wh.jpg" alt="WeChat5af5c56638f9e3095f8cf6d1bbb2e6af.png" style="zoom: 33%;" />

## 缓存穿透(数据库没有)

**描述**

**缓存穿透**是指**缓存和数据库都没有的数据**，**而用户不断发起请求**，如发起id为"-1"的数据或id为特别大的不存在的数据。这时的用户很可能是攻击者，导致服务器压力过大。

**解决方案**

1. **接口层增加校验**

   如**用户鉴权校验**，**参数范围校验**，id<=0的直接拦截。

2. **布隆过滤器**(重点)

   在访问缓存层和存储层之前，可以通过**定时任务**或者**系统任务**来**初始化布隆过滤器**，**将存在的key用布隆过滤器提前保存**起来，做**第一层拦截**。

3. **缓存空值**

   从**缓存取不到数据，在数据库中也没有取到**，这时也可以**将key-value对写为key-null，缓存有效时间可以设置短点**，如30秒(**设置太长会导致正常使用情况也没法使用**)。这样可以**防止攻击用户反复用同一个id暴力攻击**。



## 缓存击穿(数据库有)

**描述**

**缓存击穿**指**缓存中没有**但**数据库中有**的数据(一般是**缓存时间到期**)，这时由于并发用户特别多，同时读缓存没读到数据，又**同时去数据库取数据**，引起数据库压力瞬间增大，造成过大压力。

**解决方案**

1. 设置**热点数据永不过期**。
2. **加互斥锁**，代码如下：

```java
public class Cache {
    // ReentrantLock 锁
    ReentrantLock reentrantLock = new ReentrantLock();
    public  String getData(String key) {
        // 从缓存读
        String result = getDataFromRedis(key);
        // 如果缓存不存在数据
        if(result==null){
            // 去获取锁 获取成功，去数据库读数据
            if(reentrantLock.tryLock()){
                try {
                    // 从数据库获取数据
                    result = getDataFromMysql(key);
                    // 更新缓存信息
                    if (result != null) {
                        setDataToCache(key, result);
                    }
                }catch (Exception e){
                }finally {
                    // 锁必须在finally释放，不然如果出现写异常，锁永远打不开了
                    reentrantLock.unlock();
                }
            }
            // 获取锁失败
            else {
                // 暂停100ms后重新去获取数据
                Thread.sleep(100);
                result = getData(key);
            }
        }
        return result;
    }
}
```



## 缓存雪崩

**描述**

**缓存雪崩**是指缓存中**数据大批量到过期时间**，而**查询量巨大**，引起数据库压力过大。和缓存击穿不同的是，**缓存击穿指并发查询同一条数据**，**缓存雪崩是不同数据都过期了**，很多数据都查不到从而查数据库。

**解决方案**

1. **缓存数据的过期数据设置随机**，防止同一时间大量数据过期现象发生。
2. 如果缓存数据库是**分布式集群部署**，将**热点数据均匀分布在不同的缓存数据库中**。
3. 设置**热点数据永不过期**。



## 布隆过滤器

```java
public class BloomFilter {
    // 布隆过滤器容量
    private static final int DEFAULT_SIZE = 2<<28;
    // bit数组,用来存放结果
    private static BitSet bitSet = new BitSet(DEFAULT_SIZE);
    // 扰乱值：后面hash函数会用，用来生成不同hash值，可随意设置
    private static final int[] hashInts = {1,6,16,38,58,68};

    // add方法,计算key的hash值，并将对应下标设置为true
    public void add(Object key){
        for(int i:hashInts)
            bitSet.set(hash(key,i));
    }

    /**
     * 判断key是否存在
     * true不一定说明key存在 （哈希冲突）
     * 但是false一定说明不存在！
     */
    public boolean isContain(Object key){
        boolean result = true;
        for(int i:hashInts)
            // 短路与，只要有一个bit位为false,则返回false
            result = result && bitSet.get(hash(key,i));
        return result;
    }

    // hash函数，借鉴了hashmap的扰动算法；强烈建议看懂
    // 通过不同i去扰乱最终key的hash值
    private int hash(Object key, int i){
        int h;
        return  key==null?0:((DEFAULT_SIZE-1)&((h=key.hashCode()*i))^(h>>>16));
    }

    public static void main(String[] args) {
        BloomFilter bloomFilter = new BloomFilter();
        bloomFilter.add("张学友");
        bloomFilter.add("郭德纲");
        bloomFilter.add("蔡徐鸡");
        bloomFilter.add(666);
        System.out.println(bloomFilter.isContain("张学友"));//true
        System.out.println(bloomFilter.isContain("张学友 "));//false
        System.out.println(bloomFilter.isContain("张学友1"));//false
        System.out.println(bloomFilter.isContain("郭德纲"));//true
        System.out.println(bloomFilter.isContain("蔡徐老母鸡"));//false
        System.out.println(bloomFilter.isContain(666));//true
        System.out.println(bloomFilter.isContain(888));//false
    }
}
```

**布隆过滤器如何初始化？**

在访问缓存层和存储层之前，可以通过**定时任务**或者**系统任务**来**初始化布隆过滤器**，**将存在的key提前用布隆过滤器保存起来**，做**第一层拦截**。



**为什么一个key要生成多个hash?**

因为如果一个key生成一个哈希值，很可能出现**两个不同key生成了同一个hash值**。**哈希冲突**。

通过**扰动数组结合key**，生成**多个hash值**，两个不同的key生成的多个hash值**很小概率同时相等**。



**布隆过滤器返回的true和false代表着什么？**

- 返回为**true**时，**key不一定存在**

  虽然布隆过滤器一个key结合扰动数组生成多个hash对比很大程度上降低了哈希冲突的概率，但还是**有极小可能出现多个hash值同时冲突**。

- 返回为**false**时，**key一定不存在**

  多个hash值进行短路与操作，其中一个为false,就能说明key不存在。

------

## BitSet

Java中，面向程序员操作的最小数据粒度是byte，BitSet提供了bit方法。

> 使用

原理，BitSet底层是long数组，long范围是2^63-1，所以**1个long能表示64个bit位(2^6)**.

```java
BitSet bitSet = new BitSet(1024);
bitSet.set(1023);
System.out.println(bitSet.toLongArray().length); // (1023+1)/64 = 16
// 此时1024>最大idx1023,会自动扩容
bitSet.set(1024);
System.out.println(bitSet.toLongArray().length); // (1024+1)/64 = 17
```

BitSet支持的**最大空间为Interger.MAX_VALUE**, 也就是 2^31-1个bit。所以，一个**BitSet最大占用内存约为0.25GB**.

> 面试题：如何从2.5亿个数字中判断是否存在某个数字？

2.5亿约等于2^28, 如果采用布隆过滤器，判断一个数字假如生成8个不同hash值映射到BitSet上，一共要2^31个bit, 刚好约等于一个BitSet大小。但此时，BitSet不碰撞情况下0到2^31-1的bit位全是true. 碰撞率100%。

解决方法，构建16个BitSet, 通过idx = num & 1111 判断进来的数字映射到哪个BitSet,同时get方法时也通过index去对应的BitSet判断。此方法能实现低误判率。

注意： 16个BitSet占用0.25GB * 16 = 4GB, 要在JVM设置内存大小大于4GB，不然出现OOM

```java
-Xms5g -Xmx5g
```