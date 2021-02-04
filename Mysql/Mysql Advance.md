### Mysql 架构介绍

#### 层次结构

1. 连接层
2. 服务层
3. 引擎层
4. 存储层

#### 存储引擎

- InnoDB
- MyISAM

|        | MyISAM                     | InnoDB                                                   |
| ------ | -------------------------- | -------------------------------------------------------- |
| 主外键 | 不支持                     | 支持                                                     |
| 事务   | 不支持（偏向查询）         | 支持                                                     |
| 行表锁 | 表锁，不适合高并发操作     | 行锁，操作时只锁某一行，适合高并发                       |
| 缓存   | 只缓存索引，不缓存真实数据 | 缓存索引和数据，对内存要求高，内存大小对性能有决定性影响 |
| 表空间 | 小                         | 大                                                       |
| 关注点 | 性能                       | 事务                                                     |



------

### 索引优化分析

#### sql性能下降原因

- 执行时间长
- 等待时间长



1. 查询语句写的不行
2. 索引失效
   - 单值
   - 复合
3. 关联查询太多join
4. 服务器调优及各个参数设置（缓冲、线程数等）



#### 常见通用的Join查询

**SQL执行顺序**

```mysql
# 人写
select distinct
from
join  on
where
group by
having
order by
limit
```

```mysql
# 机读
from  # 多个表的话， 笛卡尔积
on  join
where 
group by
having
select distinct
order by
limit
```

```mysql
inner join # 两者公有部分
left join # 左表全部，未满足部分补null

# 全外连接（mysql不支持）
left join 
union   # 能去重
right join
```



#### 索引简介

索引是排好序的快速查找**数据结构**

除了数据外，数据库系统还维护着满足特定**查找算法的数据结构**。

**多路搜索树** (B+ Tree)

查询快，增删慢。

 增删操作除了改数据，还要改索引！

频繁删改的字段不适合建索引

索引往往以索引文件的形式存储在磁盘上



**索引优势**

- 提高了数据**检索**效率，降低了数据库的**IO**成本
- 通过索引对数据进行排序，降低了数据**排序成本**，降低了**CPU**消耗

**索引劣势**

- 索引实际上类似一张表，表保存了主键和索引字段，并指向实体表的记录。**占用磁盘空间**。
- 虽然索引**提高了查询速度**，但却**降低了更新速度**。修改时，Mysql不仅修改数据，还会调整因为更新带来的键值变化后的索引信息。

------



**索引分类**

- 单值索引：一个索引只包含**单个列**，一个表可以有多个单列索引。
- 唯一索引：索引列的值必须**唯一**，但允许空值
- 复合索引：一个索引包含**多个列**。

```mysql
create [unique] index indexName on mytable(columnname(length))
alter mytable add [unique] index[indexName] on (columnname(length))
drop index[indexName] on mytable
show index from table_name;
```

------

**索引结构**

[![yFPOjU.png](https://s3.ax1x.com/2021/01/29/yFPOjU.png)](https://imgchr.com/i/yFPOjU)

**查找过程**

如果要查找数据项29，首先把磁盘块1由磁盘加载到内存，此时发生一次IO，在内存中用**二分查找**确定29在17和35中间，锁定磁盘块的指针P2，内存时间因为非常短(相比磁盘IO)可以忽略不计，通过磁盘块1的指针P2的磁盘地址把磁盘块3由磁盘加载到内存，发生第二次IO，29在26和30之间，锁定磁盘块3的指针P2，通过指针加载磁盘块8到内存，发生第三次IO，同时在内存中做二分查找找到29，结束查询，总计三次IO。

------

**哪些情况需要创建索引**

- **主键**自动建立唯一索引
- 频繁作为**查询条件**的字段应该创建索引
- 查询中与其它表**关联的字段**，外键关系建立索引
- 查询中的**排序**字段建立索引，大大提高排序速度

- 查询中**统计**或者**分组**(group by)字段



**哪些情况不需要创建索引**

- 表记录太少 （500万）
- 经常增删改的表
- 某个列大部分重复，分布概率相似，没必要创建索引( 性别，国籍)

------

#### 性能分析

**Mysql Query Optimizer**



**MySQL 常见瓶颈**

- CPU: CPU饱和一般发生数据装入内存和从磁盘读取数据的时候
- IO：磁盘I/O瓶颈发生在装入数据远大于内存容量的时候
- 服务器硬件： top, free, iostat和vmstat来查看系统性能状态



**Explain**

------

**Explain介绍**

使用explain关键字可以模拟优化器执行SQL查询语句，从而知道MySQL是如何处理你的SQL语句的。可以用来分析查询语句。



**Explain 作用**

- 表的读取顺序（id）
- 数据读取操作的操作类型 （select type）
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询 (rows)



**Explain + SQL**

- id

  - id相同，执行顺序由上向下
  - id不同，如果是子查询，id的序号会递增，id值越大优先级越高，越先被执行。
  - id相同不同，同时存在
    - 先走数字大
    - 数字一样平级顺序执行

- select_type 

  查询的类型，主要区别普通查询、联合查询、子查询等复杂查询

  - simple

     简单的select查询，不包含子查询或者union。

  - primary

    查询中若包含有复杂的子成分，最外层查询标记为primary。

  - subquery

    在select或者where列表中包含了子查询

  - derived

    在from列表中包含的子查询被标记为DERIVED，mysql会递归执行这些子查询，把结果放在临时表里。

  - union

    若第二个select出现在union之后，被标记为union；

  - union result

    从union表获取的结果

- table 

  显示这一行的数据是关于哪张表的

- type

  显示查询使用了何种类型：

  从最好到最差依次是：

  **system>const>eq_ref>ref>range>index>ALL**

  - system

    表只有一行记录(等于系统表)，这是const类型的特例。

  - const

    通过索引一次就找到了，const用于比较**primary key**或者**unique**索引。因为**只匹配一行**数据，所以很快。

  - eq_ref

    **唯一性索引**扫描，对于每个索引键，表中只有**一条记录**与之匹配，常见于主键或唯一索引扫描。

  - ref

    **非唯一性索引**扫描，返回匹配某个单独值的**所有行**。

  - range

    只**检索给定范围**的行，使用一个索引来选择行。

    一般是where语句出现了between、>、<

    这种范围扫描索引比全表扫描要好，因为它只需要开始于索引某一点，而结束于另一点，不用扫描全部索引。

  - index

    Full Index Scan, index与All区别为index只遍历**索引树**。这通常比ALL快，因为索引文件通常比数据文件小。(和ALL都是全表扫描，区别是index从索引(一般在内存)中读取，而all是从硬盘读取)

  - all 

    Full Table Scan,将便利全表找到匹配的行

**一般来说，至少保证查询达到range级别，最好能到ref.**



- possible_keys

  显示可能应用在这张表的索引，一个或多个。

  查询涉及到字段上是否存在索引。但**不一定被查询实际使用**

- key

  **实际用到的索引**，如果为NULL，则没有使用索引。

  查询中若出现**覆盖索引**，则该索引只出现在key列表中。

  覆盖索引： select的字段和建立复合索引的字段的个数和顺序一致。(从索引就找到返回值)

- key_len

  索引字段的最大可能长度，并非实际长度。不损失精确度的情况下，长度越短越好。

- ref

  显示索引的那一列被使用了。

- rows

  根据表统计信息及索引选用情况，大致估算出找到所需记录所需要读取的行数。

- Extra

  包含不适合在其他列中显示但十分重要的额外信息。

  - *Using filesort 

    说明mysql会对数据使用一个外部的索引排序。而不是按照表内的索引顺序进行读取。

    MySQL中无法利用索引完成的排序称为“**文件排序**”。

  - *Using temporary

    使用了临时表保存中间结构，MySQL在对查询结果排序时使用临时表。常见于order by和分组查询group by。

  - *Using index

    表示相应的select操作使用了**覆盖索引(Covering Index)**，避免访问数据行，效率不错。需要读索引文件，不需要再根据索引读数据文件。

    如果使用覆盖索引，一定要注意select列表只取需要的列，不要select *

  - Using where

    用了where过滤

  - Using join buffer

    使用了连接缓存

  - impossible where

    where的值永远等于false

------

**案例优化**

```mysql
# 建立联合索引
create index idx_article_ccv on article(catogory_id,comments,views)
# 本处type是range，但会出现 using filesort（中间的索引是范围的情况下，后面排序失效）
explain select id,author_id from 'article' where catogory_id=1 and comments>1 order by views desc limit 1;
# 本处不会出现 using filesort
explain select id,author_id from 'article' where catogory_id=1 and comments=1 order by views desc limit 1;

# 结论
# type 变成range，可以忍受，但extra里面的Using filesort无法接受
# 我们建立了索引 为什么没用？
# BTree 索引工作原理
# 先排序 catogory_id，
# 如果catogory_id相同排序comments,如果comment相同再排序views
# 当comments字段在联合索引里处于中间位置时
# 因为comments>1条件是一个范围值(range)
# MySQL无法利用索引对后面views部分进行检索，即range类型查询字段后面的索引无效。

# 解决方法
# 丢弃不合适索引
drop index idx_article_ccv on article;
# 建立新索引
create index idx_article_ccv on article(catogory_id,views)
# 此时， type变成了ref, 同时extra里面的using filesort消失了
explain select id,author_id from 'article' where catogory_id=1 and comments>1 order by views desc limit 1;
```

```mysql
# 双表

# 左右连接（相反加）

# left join条件用于确定如何从右表搜索行，左边一定都有
# 所以右边是关键点，一定需要建立索引。

```

```mysql
# 三表
# 模仿双表，执行加2次index
```

------

案例：索引失效

1. 全值匹配我最爱

2. 最佳左前缀法则

   如何使用复合索引，要遵守最左前法则。查询从索引的**最左前列**开始并且**不跳过**索引中的列。如果**中间缺少**，能使用到**前面部分索引**。

   (**火车跑得快 全靠车头带**)
   (**中间兄弟不能断**)

3. 不在索引列上左任何操作（计算、函数、类型转换（手动、自动）），会导致索引失效而转向全表扫描

   **索引列上无计算**

4. 存储引擎不能使用索引中范围条件右边的列

   **范围之后全失效**

5. 尽量使用**覆盖索引**（只访问索引的查询（索引列和查询列一致）），减少select *。

6. is null, is not null也无法使用索引。

7. like以通配符开头('%abc')mysql索引失效会变成全表扫描的操作。

   **like百分加右边**

   **问题：解决 like '%字符串%' 时索引不被使用的方法？**

   用**覆盖索引**解决，可以避免全表扫描。

8. 字符串不加单引号索引失效。

   可能会被**强制类型转换，索引失效**。

   **字符串里有引用**

9. 少用or,用它来连接时索引失效。

------

#### 索引面试题

```mysql
create index idx_test03_c1234 on test03(c1,c2,c3,c4);
# 本处顺序1 2 4 3,但优化器会优化成1234，优化器自动排序
explain select * from test03 where c1='a1' and c2='a2' and c4='a4' and c3='a3';

# 用到3个， 范围之后c4失效
explain select * from test03 where c1='a1' and c2='a2' and c3>'a4' and c4='a3';

# 用到2个！ c1,c2   (索引有两个功能，查找和排序   本处expain只显示用于查找的c1,c2.         c3用于排序)
explain select * from test03 where c1='a1' and c2='a2' and  c4='a3' order by c3;

# 用到2个！ c1,c2   (索引有两个功能，查找和排序   本处expain只显示用于查找的c1,c2.         c3用于排序)
explain select * from test03 where c1='a1' and c2='a2' order by c3;

# using file sort  .  c4的索引失效
explain select * from test03 where c1='a1' and c2='a2'  order by c4;

# 本处不会出现using file sort,  c1用于查询 c2 c3用于排序
explain select * from test03 where c1='a1' and c5='1'  order by c2,c3;

# using file sort . c3 c2 索引顺序  导致索引失效
explain select * from test03 where c1='a1' and c5='1'  order by c3,c2;

# 正常运行
explain select * from test03 where c1='a1' and c2='a2'  order by c2,c3;

# 正常运行，本出c2前面已经出现，c2变成常量！  后面c3可以走索引
explain select * from test03 where c1='a1' and c2='a2'  order by c3,c2;

# using temporary, using filesort
explain select c1 from test03 where c1='a1' and c5='1'  group  by c3,c2;

# 正常
explain select c1 from test03 where c1='a1' and c5='1'  group  by c2,c3;

# 只用到c1
explain select c1 from test03 where c1='a1' and c2='%kk%' and c3='a3';

# 用到c1,c2,c3
explain select c1 from test03 where c1='a1' and c2='k%kk%' and c3='a3';
```

group by基本上需要进行排序，会有临时表产生

------

#### 查询优化

- 对于单键索引，尽量选择针对当前query过滤性更好的索引
- 在选择复合索引，当前query中过滤下最好的字段在索引字段顺序中，位置越靠前越好。
- 在选择复合索引的时候，尽量选择可以包含当前query中where句子中更多字段的索引。
- 尽可能通过分析统计信息和调整query的写法来达到合适索引的目的。

------



### 查询截取分析

总结

1. 慢查询日志开启并捕获
2. explain+慢SQL分析
3. show profile查询SQL在Mysql服务器里面执行细节和生命周期情况
4. SQL数据库服务器的参数调优

------

#### 查询优化



**永远小表驱动大表**



for(int i=5;....){

​		for(int j=1000){

​		}

}

```mysql
select * from A where id in (select id from B)
#等价于
for select id from B:
		for select * from A where A.id=B.id
# 当B表数据集必须小于A表时，用in优于exists


select * from A where exists(select 1 from B where B.id=A.id)
# 等价于
for select * from A
		for select * from B where B.id = A.id
# 当A表数据集小于B表时，exists优于in
```





**Order by 关键字优化**









------









#### 慢查询日志

#### 批量数据脚本

#### Show Profile

#### 全局查询日志







### Mysql锁机制



### 主从复制

