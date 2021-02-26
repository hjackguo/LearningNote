## Redis数据类型

- String

- List

- Set

- Hash

- Zset

  有序集合。

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
        return  key==null?0:(i*(DEFAULT_SIZE-1)&((h=key.hashCode()))^(h>>>16));
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

