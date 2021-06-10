# NoSQL概述

> NoSQL指什么？

No Only SQL. 泛指非关系型数据库。 有些类型的数据存储(人际网络)不需要固定的模式，无需多余操作就可以横向拓展。



> 为什么要NoSQL?

![WeChat03fa68f2d87beaf6de8621542f827fcc](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChat03fa68f2d87beaf6de8621542f827fcc.png)

![WeChat6cead5ec49ba9cef3dce7c7c60f2143b](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChat6cead5ec49ba9cef3dce7c7c60f2143b.png)

主从机，读写分离

![WeChatf7e3427bab6fc1421126be21ae3cc33e](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChatf7e3427bab6fc1421126be21ae3cc33e.png)



后面的优化方向往集群走，把不同的业务分到不同集群。如注册数据和业务数据是放置在不同集群上。

![WeChat81ecf05f34e2213c937599a32b8997e9](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChat81ecf05f34e2213c937599a32b8997e9.png)

NoSQL给架构中加了Cache层。

在上面的优化后，可以采用分表结构。编号1-3000万在一个库，3000万-6000万在另一个库。

![WeChatb13a81c87e872c64bafe192f73ed90bf](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChatb13a81c87e872c64bafe192f73ed90bf.png)



> NoSQL能干嘛？

- 易扩展

  NoSQL数据库种类繁多，但共同的特点都是去掉关系数据库的关系型特性。数据之间无关系，这样就非常容易扩展，也在架构层面上带来了可扩展能力。

- 大数据量高性能

  一般MySQL使用Query Cache，每次表的更新Cache就失效，是一种大粒度的Cache，性能不高。NoSQL的Cache是记录级的，是一种细粒度的Cache，所以NoSQL在这个层面上来说就要性能高很多。

- 多样灵活的数据类型

  NoSQL无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。而在关系型数据库里，增删字段是一件非常麻烦的事情。

- 传统RDBMS VS NOSQL

  - RDBMS
    - 高度组织化结构数据
    - 结构化查询语句 SQL
    - 数据和关系都存储在单独的表中
    - 数据操纵语言，数据定义语言
    - 严格的一致性
    - 基础事务
  - NoSQL
    - 代表着不仅仅是SQL
    - 没有声明性查询语言
    - 没有预定义的模式
    - 键-值对存储，列存储，文档存储，图形数据库
    - 最终一致性，而非ACID属性
    - 非结构化和不可预知的数据
    - CAP定理
    - 高性能，高可用和高伸缩性

  

> 3v + 3高

- 大数据时代的3V
  - 海量Volume
  - 多样Variety
  - 实时Velocity
- 互联网需求的3高
  - 高并发
  - 高可扩
  - 高性能



# 当下NoSQL应用场景

> 淘宝商品

- 商品基本信息
  - 名称，价格等
  - 关系型数据库
  - 淘宝的MySQL是改造过的
- 商品描述、详情、评价信息(多文字类)
  - 多文字信息描述类，IO读写性能变差
  - 文档数据库MongDB中
- 商品的图片
  - 分布式的文件系统中
    - 淘宝自己的TFS
    - Google的GFS
    - Hadoop的HDFS
- 商品的关键字
  - 搜索引擎，淘宝内用
  - ISearch
- 商品的波段性的热点高频信息
  - 内存数据库
  - Tair 、Redis、 Memcache
- 商品的交易、价格计算、积分累计



> 总结大型互联网应用(大数据、高并发、多样数据类型)的难点和解决方案

- 难点
  - 数据类型多样性
  - 数据源多样性和变化重构
  - 数据源改造而数据服务平台不需要大面积重构
- 解决方案
  - 统一数据平台服务层
  - 阿里、淘宝干了什么？ UDSL
    - 映射
    - API
    - 热点缓存

![WeChat840a61d1d7de9cc900ee9c1c702153b7](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChat840a61d1d7de9cc900ee9c1c702153b7.png)



# NoSQL数据模型

![WeChat9dbad2271ca8ad938be97b891a27d1a61](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChat9dbad2271ca8ad938be97b891a27d1a61.png)



> 面试题：以一个电商客户、订单、订购、地址模型来对比下关系型数据库和非关系型数据库

**关系型数据库**

![WeChat4453afd0495a10529447ee77e18bbd7c](https://cdn.jsdelivr.net/gh/HuanjieGuo/PicGo/img/WeChat4453afd0495a10529447ee77e18bbd7c.png)

**非关系型数据库**

![WeChat4c9f02dc3c0d9a47dde2c0e9539c2e9c](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChat4c9f02dc3c0d9a47dde2c0e9539c2e9c.png)



**为什么上述情况可以用聚合模型处理**？

- 高并发的操作是不太建议有关联查询的，互联网公司用冗余数据来避免关联查询
- 分布式事务是支持不了太多的并发



**BSON如何查**？

文本中查找id, 拿出整个字典



> 聚合模型

- KV键值
- Bson
- 列族

![WeChat020df6187c37c1fe45da583a94aa727d](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChat020df6187c37c1fe45da583a94aa727d.png)



- 图形

![WeChatd233079f0064e6db67e94211d39bf411](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChatd233079f0064e6db67e94211d39bf411.png)





# NoSQL数据库的四大分类

- KV键值
  - 阿里、百度： memcache + redis
  - 美团： redis+tair
- 文档型数据库(bson格式较多)
  - MongoDB
    - 基于分布式文件存储的数据库。
    - 是一个关系型与非关系型数据库之间的产品
- 列存储数据库
  - HBase
  - 分布式文件系统
- 图关系数据库
  - 不是放图形的，放的是关系比如：朋友圈社交网络，广告推荐系统网络等。专注于构建关系图谱。
  - Neo4J, InfoGrid



# 分布式的数据库CAP+BASE (*)

> 传统的ACID分别是什么

- Atomicity 	原子性
- Consistency   一致性
- Isolation     独立性
- Durability   持久性



> CAP

- C : Consistency (强一致性)
- A : Availability (可用性)
- P : Partition tolerance (分区容错性) 



CAP理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性这三个需求，**最多只能同时较好的满足两个**。

因此，根据CAP原理将NoSQL数据库分成了满足CA原则、满足CP原则和满足AP原则三大类：

CA - 单点集群，满足一致性，可用性的系统，通常在可扩展性上不太强大。

CP - 满足一致性，分区容忍性的系统，通常性能不是特别高

AP - 满足可用性，分区容忍性的系统，通常可能对一致性要求低一点。

![WeChat32f6d4b4c51a0360ea09e4ad312ef358](https://picgohuanjie.oss-cn-beijing.aliyuncs.com/img/WeChat32f6d4b4c51a0360ea09e4ad312ef358.png)





> CAP的3进2

CAP理论就是说在分布式存储系统中，最多只能实现上面的两点。由于当前的网络硬件肯定会出现延迟丢包等问题，所以**分区容忍性是我们必须实现的**。

所以我们只能在**一致性**和**可用性**之间进行权衡，没有NoSQL系统能同时保证这三点。

---------------

C: 强一致性     A：高可用性    P：分布式容忍性

CA：传统Oracle数据库

AP： 大多数网络架构的选择 (电商等的选择，保证系统正常运行，对浏览量等不要求强一致性)

CP： Redis、 MongoDB



**一致性与可用性的抉择**

对于web2.0网站来说，关系数据库的很多主要特性却往往无用武之地。



**数据库事务一致性需求**

很多web实时系统并不要求严格的数据库事务，对读一致性的要求很低，有些场合对写一致性要求并不高，允许实现最终一致性。



**数据库的写实时性和读实时性需求**

对关系数据库来说，插入一条数据之后立刻查询，是肯定可以读出来这条数据的，但是对于很多web 应用来说，并不要求这么高的实时性，比方说发一条消息之后，过几秒乃至十几秒后，我的订阅者才看到这条动态是完全可以接受的。



**对复杂的SQL查询，特别是多表关联查询的需求**

任何大数据量的web系统，都非常忌讳多个大表的关联查询，以及复杂的数据分析类型的报表查询，特别是SNS类型的网站，从需求以及产品设计角度，就避免了这种情况的产生。往往更多的只是单表的主键查询，以及单表的简单条件分页查询，SQL的功能被极大的弱化了。



> BASE

BASE就是为了解决关系数据库强一致性引起的问题而引起的可用性降低而提出的解决方案。



BASE其实是下面三个术语的缩写：

- 基本可用 (Basically Available)
- 软状态 (Soft state)
- 最终一致 (Eventually consistent)



它的思想是**通过让系统放松对某一时刻数据一致性的要求来换取系统整体伸缩性和性能上改观**。为什么这么说呢，缘由就在于大型系统往往由于地域分布和极高性能的要求，不可能采用分布式事务来完成这些指标，要想获得这些指标，我们必须采用另外一种方式来完成，这里BASE就是解决这个问题的方法。



> 分布式+集群简介

**分布式**： 不同的多台服务器上面部署**不同**的服务模块(工程)，他们之间通过RPC/RMI通信和调用，对外提供服务和组内写协作。

**集群**：不同的多台服务器上面部署**相同**的服务模块，通过分布式调度软件进行统一的调度，对外提供服务和访问。



# Redis配置

mac中路径  /usr/local/redis-6.2.1

创建的配置文件位置 

```shell
/Users/guohuanjie/Documents/setting/redis-setting/redis.conf
```

查看redis运行情况

```shell
ps -ef|grep redis
```



# Redis启动后的杂项基础知识

> 单进程

单进程模型来处理客户端的请求。对读写等事件的响应

是通过对epoll函数的包装来做到的。Redis的实际处理速度完全依靠主进程的执行效率

**Epoll**是Linux内核为处理大批量文件描述符而做了改进的epoll，是Linux下**多路复用IO**接口select/poll的增强版本，它能显著提高程序在大量并发连接中只有少量活跃的情况下的系统CPU利用率。



> 命令

**切换数据库**

默认有16个数据库0-15

```shell
select index
```



**查看当前数据库key的数量**

```
dbsize
```



**清除keys**

```java
// 清除当前数据库
FLUSHDB

// 清除所有数据库
FLUSHALL
```



# 常用五大数据类型

## zset

sorted set: 有序集合

Redis zset和set一样也是string类型元素的集合，且不允许重复的成员。

**不同的是每个元素都会关联一个double类型的分数**。

redis正是通过分数来为集合中的成员进行从小到大的排序。**zset成员是唯一的，但分数(score)却可以重复**。

```shell
127.0.0.1:6379> zadd zset01 60 v1 70 v2 50 v3 20 v4
(integer) 4
127.0.0.1:6379> zrange zset01 0 -1
1) "v4"
2) "v3"
3) "v1"
4) "v2"
127.0.0.1:6379> zrange zset01 0 -1 withscores
1) "v4"
2) "20"
3) "v3"
4) "50"
5) "v1"
6) "60"
7) "v2"
8) "70"
127.0.0.1:6379> ZRANGEBYSCORE zset01 20 50
1) "v4"
2) "v3"
127.0.0.1:6379> zrem zset01 v3
(integer) 1
127.0.0.1:6379> zcard zset01
(integer) 3
127.0.0.1:6379> zcount zset01 20 50
(integer) 1

```

