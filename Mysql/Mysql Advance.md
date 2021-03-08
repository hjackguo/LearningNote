### Mysql三大范式

#### 第一范式

1NF是对属性的原子性，要求属性具有**原子性**，不可再分解；

> 表：字段1、字段2 (字段2.1、字段2.2)、字段3 ...

如学生 (学号，姓名，性别，出生年月日)，如果认为最后一列还可以再分成(出生年，出生月，出生日)，它就不是第一范式了，否则就是。



#### 第二范式

2NF是对记录的**唯一性**，要求记录有唯一标识，即实体的唯一性，即不存在部分依赖。

> 表：学号、课程号、姓名、学分、成绩

这个表明显说明了两个事物: 学生信息，课程信息；由于非关键字段必须依赖主键，这里学分依赖课程号，姓名依赖于学号，所以不符合第二范式。

可能存在问题：

- **数据冗余**：每条记录都含有相同信息；
- **删除异常**：删除了所有学生成绩，就把课程信息全删除了；
- **插入异常**：学生未选课，无法记录进数据库；
- **更新异常**：调整课程学分，所有行都调整。

正确做法：

学生：**Student**(学号，姓名)

课程：**Course**(课程号，学分)

选课关系：**StudentCourse**(学号，课程号，成绩)



#### 第三范式

3NF是对字段的**冗余性**，要求任何字段不能由其他字段派生出来，它要求字段没有冗余，即不存在传递依赖；

> 表：学号，姓名，年龄，学院名称，学院电话

因为存在依赖**传递**：学号->姓名->所在学院->学院电话

可能会存在的问题：

- 数据冗余：有重复值；
- 更新异常：有重复的冗余信息，修改时需要同时修改多条记录，否则会出现**数据不一致的情况**。

正确做法：

学生：(学号，姓名，年龄，所在学院)

学院：(学院，电话)





> 范式化

**优点**

- 可以尽量的减少数据冗余，数据表更新快体积小
- 范式化的更新操作比反范式化更快

**缺点**

- 对于查询需要对多个表进行关联，导致性能降低
- 更难进行索引优化



> 反范式化

一般来说，数据库只需满足第三方式(3NF)就行了。没有冗余的数据库设计是可以做到的，但是没有冗余的数据库未必是最好的数据库。有时为了提高运行效率，必须降低范式标准，适当保留冗余数据。

**优点**

- 可以减少表的关联
- 可以更好的进行索引优化

**缺点**

- 存在数据冗余及数据维护异常
- 对数据的修改需要更多的成本

------



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

### 事务

#### ACID

- Atomic

  原子性

- Consistency

  一致性

- Isolation

  隔离性

- Durability

  持久性



#### InnoDB事务问题

| 隔离级别                       | 读数据一致性                             | 脏读 | 不可重复读 | 幻读 |
| ------------------------------ | ---------------------------------------- | ---- | ---------- | ---- |
| 未提交读 (Read uncommitted)    | 最低级别，只能保证不读取物理上损坏的数据 | 是   | 是         | 是   |
| 已提交读(Read committed)       | 语句级                                   | 否   | 是         | 是   |
| 可重复读(Repeatable read) 默认 | 事务级                                   | 否   | 否         | 是   |
| 可序列化(Serializable)         | 最高级别，事务级                         | 否   | 否         | 否   |



#### 事务实现原理(log)

事务常说一系列操作作为一个整体，要么都成功要么都失败，主要特征ACID，事务的实现主要依赖**redo-log**和**undo-log**，每次事物都会记录**修改前的undo-log**，**修改后的数据放入redo-log**,提交成功则使用redo-log更新到磁盘，失败则用undo-log将数据恢复到事物之前的数据。



#### MVCC

**多版本并发控制**。在InnoDB中，会在每行数据后添加两个额外的隐藏的值来实现MVCC,这两个值一个记录这行数据何时被创建，另一个记录这行数据何时过期(或者被删除)。实际操作中，存储的不是时间，而是事务的版本号，每开启一个新事务，事务的版本号加1。这样保证了每个事务操作的数据，都是互不影响的，也不存在锁问题。



> 如果多个事务操作同一条数据并发过程中，谁先成功？

mysql判断，谁先提交算成功。

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
- 聚簇索引：一般来说，聚簇索引就是主键，聚簇索引的叶子结点中，存着主键对应行的所有数据，通过聚簇索引查询，不需要回表。

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

3. 不在索引列上做任何操作（计算、函数、类型转换（手动、自动）），会导致索引失效而转向全表扫描

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



**问：Mysql索引为什么要用B+树而不是B树呢？**

B树不管叶子结点还是非叶子结点，都会保存数据，这样导致在非叶子结点中能保存的指针数量变少。 指针少的情况下要保存大量数据，**只能增加数的高度**，导致IO操作变多，查询性能变低。

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

```mysql
# 不会出现filesort
explain select * from tblA where age > 20 order by age,birth;

# 出现file sort
explain select * from tblA where age > 20 order by birth;

# 出现filesort
explain select * from tblA where age > 20 order by birth,age;

# 出现filesort
explain select * from tblA order by age ASC, birth desc;

```



Filesort排序有两种：双路排序和单路排序

**单路排序**

从磁盘读取查询需要的所有列，按照order by列在buffer对它们进行排序，然后扫描排序后的列表进行输出，它的效率比双路排序更快一些，避免了二次读取数据。并且把随机IO变成了顺序IO，但是它会使用更多的空间，因为它把每一行都保存在内存中了。



- 单路算法是后面出现的，总体优于双路排序。

- 但是如果读取数据量过大，单路算法需要多次IO读取，效果比双路算法差。

  在sort_buffer中，单路比双路要多占用很多空间，因为单路是把所有字段取出，所以有可能取出的数据总大小超出了sort_buffer容量，导致每次只能读取sort_buffer容量大小的数据进行排序(创建tmp文件，多路合并)，排完再取sort_buffer容量大小，再排... 从而产生多次IO。

  本来单路算法是想省一次IO操作，可能反而导致了大量I/O操作。



**提高order by的速度**

- 用order by的时候不要用select  *

  当Query的字段大小总和小于max_length_for_sort_data并且排序字段不是TEXT|BOLB类型时，会用改进后的算法 — 单路排序，否则用老算法—多路排序。

  两种算法数据都可能超出sort_buffer容量，超出之后，会创建tmp文件进行合并排序，导致多次I/O，但是用单路排序算法的风险大一点，所以提高max_buffer_size.

- 增大sort_buffer_size参数的设置
- 增大max_length_for_sort_data参数的设置





**为排序使用索引**

- Mysql两种排序方式：文件排序或扫描有序索引排序
- Mysql能为排序于查询使用相同的索引



![1881612464003_.pic_hd.jpg](http://ww1.sinaimg.cn/mw690/008aPpVGgy1gnc3036bxrj32fo1ss4e9.jpg)



------

#### 慢查询日志

设置long_query_time， 超过这个时间的sql会被记录到慢查询日志。

默认情况下，mysql没有开启慢查询日志，需要手动设置。

如果不是调优的话，不建议开启，会或多或少带来性能影响。

```mysql
# 查看
show variables like '%slow_query_log%';

+---------------------+--------------------------------------------+
| Variable_name       | Value                                      |
+---------------------+--------------------------------------------+
| slow_query_log      | OFF                                        |
| slow_query_log_file | /usr/local/mysql/data/MacBook-Pro-slow.log |
+---------------------+--------------------------------------------+
2 rows in set (0.11 sec)

# 开启(只对当前数据库生效，mysql重启后失效)
set global slow_query_log=1;

# 查看阈值  >10才记录
show variables like 'long_query_time%';
+-----------------+-----------+
| Variable_name   | Value     |
+-----------------+-----------+
| long_query_time | 10.000000 |
+-----------------+-----------+

#修改阈值 (需要重新连接或新建一个会话才能看到修改值)
set global long_query_time=3;

# 慢日志记录条数
show global status like '%Slow_queries%';

# 慢日志分析帮助工具
mysqldumpslow --help;
```

------



#### 批量数据脚本



具体实现查一下

------



#### Show Profile

mysql提供可以用来分析当前会话中语句执行的资源消耗情况。可以用于SQL调优的测量。

默认情况下关闭，并保存最近15次的运行结果。



1. 是否支持

2. 是否已开启 

    show variables like 'profiling'; 

3. 运行SQL

4. 查看结果 show profiles;

5. 诊断， show profile cpu,block io for query 上一步问题SQL数字号码

   show profile 出来的生命周期各项执行时间中，出现如下4个要着重看

   - coverting HEAP to MyISAM 查询结果太大，内存都不够了往磁盘上搬
   - Creating tmp table 创建临时表 （拷贝数据到临时表，用完再删除）
   - Copying to tmp table on disk 把内存中的临时表复制到磁盘，危险！！！
   - locked

<img src="http://ww1.sinaimg.cn/mw690/008aPpVGgy1gncc70rj36j30kc0t6785.jpg" alt="1891612483719_.pic_hd.jpg" style="zoom:50%;" />



------

#### 全局查询日志

只允许**测试环境**用，不允许生产环境用。

```mysql
set global general_log = 1;

set global log_output='TABLE';

select * from mysql.general_log;

# 记录时间  用户  进行了什么操作
```

------



### Mysql锁机制



**锁的分类**

从对数据操作的类型分：

- 读锁 (**共享锁**)：针对同一份数据，多个读操作可以同时进行而不互相影响。
- 写锁 (**排它锁**): 当前写操作没有完成前，它会阻断其他写锁和读锁。
  - 表锁
  - 行锁 (MyISam不支持，只有InnoDB支持)

**简而言之，读锁会阻塞写，但是不会阻塞读。而写锁会把读和写都阻塞。**



```mysql
# 加锁
lock table mylock read;
lock table mylock write;

# 释放锁
unlock tables;

# 查看各表状态
show open tables;
```

```mysql
# 读锁案例 （读阻塞写例子）

# session 1 加读锁
lock table mylock read;

# session 1和session 2 都能访问本表
select * from mylock;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  4 | d    |
|  5 | e    |
+----+------+

# session 1 读别的表 失败
select * from staffs;
# ERROR 1100 (HY000): Table 'staffs' was not locked with LOCK TABLES

# session 2 读别的表 成功
mysql> select * from staffs;
+----+------+-----+---------+---------------------+
| id | NAME | age | pos     | add_time            |
+----+------+-----+---------+---------------------+
|  1 | z3   |  22 | manager | 2021-02-03 19:12:58 |
+----+------+-----+---------+---------------------+

# session 1   update不能执行
update mylock set name='a2' where id=1;
# ERROR 1099 (HY000): Table 'mylock' was locked with a READ lock and can't be updated

# session 2 update 
 update mylock set name='a2' where id=1;  
 # 阻塞！ 等待session 1 解锁才自动执行
unlock tables;
#Query OK, 0 rows affected (0.00 sec)
# 此时session 2自动显示
#Query OK, 1 row affected (2 min 22.38 sec)
#Rows matched: 1  Changed: 1  Warnings: 0


```

```mysql
# 写锁案例

# session 1 加写锁  读 改都成功
lock table mylock write;
select * from mylock;
update mylock set name='a3' where id=1;
# session 1读其它表 失败
select * from staffs;
# ERROR 1100 (HY000): Table 'staffs' was not locked with LOCK TABLES

# session 2读其它表 成功
select * from staffs;

# session 2 读  阻塞
 select * from mylock;
 # session 2 写  阻塞
 update mylock set name='a2' where id=1;
```





```mysql
# 表锁
show status like 'table%';
+----------------------------+-------+
| Variable_name              | Value |
+----------------------------+-------+
| Table_locks_immediate      | 339   |
| Table_locks_waited         | 0     |
| Table_open_cache_hits      | 72    |
| Table_open_cache_misses    | 10    |
| Table_open_cache_overflows | 0     |
+----------------------------+-------+

# Table_locks_waited ： 出现表级锁争用而发生等待的次数，此值越高说明存在较严重的表级锁争用情况。

# MyISAM的读写锁调度是写优先，这也是myISAM不适合做写为主表的引擎，因为写锁后，其它线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永久阻塞。
```



**行锁**

偏向InnoDB，开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

InnoDB和MyISAM最大不同有两点：一是**支持事务**(TRANSACTION)；二是采用了**行级锁**。



```mysql
# 行锁演示

# session 1
set autocommit=0;
update test_innodb_lock set b='4001' where a =4;
# Query OK, 1 row affected (0.00 sec)

# session 2
set autocommit=0;
update test_innodb_lock set b='4002' where a =4;
# 阻塞 session只能  修改不同行，同一行会被阻塞

# session 1
commit;
# session 2
#Query OK, 1 row affected (5.18 sec)  等待了5秒！
# Rows matched: 1  Changed: 1  Warnings: 0
```



**无索引行锁升级为表锁**

```mysql
# session 1
set autocommit=0;
# 注意 b是varchar 写成8000,强制类型转换， 索引失效，行锁变表锁
update test_innodb_lock set a=41 where b = 8000;
# Query OK, 0 rows affected (0.00 sec)

# session 2
set autocommit=0;
update test_innodb_lock set b='5002' where a =5;
# 阻塞， 此时是表锁

# session 1
commit;
# session 2
# Query OK, 1 row affected (35.70 sec)  等待35秒
```



**间隙锁的危害**

当我们用范围条件而不是相等条件检索数据，InnoDB会给符合条件的不存在的间隙加锁。

危害： 锁定一个范围索引键值后，不存在的键值也会被锁定，造成无法插入锁定范围内的任何数据。某些场景下影响系统性能。

```mysql
mysql> select * from test_innodb_lock;
+------+------+
| a    | b    |
+------+------+
|    1 | b2   |
|    3 | 4002 |
|    4 | 4001 |
|    5 | 5002 |
+------+------+

# session 1
set autocommit=0;
update test_innodb_lock set b=4010 where a >1 and a<4;

# session 2
set autocommit = 0;
insert into test_innodb_lock values(2,'2000'); 
# 阻塞，间隙锁

# session 1
commit;
# session 2 
# Rows matched: 1  Changed: 1  Warnings: 0
```



**如何锁定一行**

```mysql
# session 1
begin;
# select *** for update 指定某一行后，其它操作会被阻塞，直到锁定行的会话提交 commit.
select * from test_innodb_lock where a=9 for update;

# session 2
 update test_innodb_lock set b='9003' where a =9; 
 # 被阻塞
 
# session 1
commit;
# session 2
# Query OK, 1 row affected (39.52 sec)
```



**总结**

**Innodb**存储引擎优于实现了**行级**锁定，虽然在锁定机制的实现方面所带来的性能损耗可能比表级锁定更高一些，但是整体**并发处理能力**方面要远远优于**MyISAM的表级**锁定的。当系统并发量较高的时候，InnoDB整体性能和MyISAM相比就会有比较明显的优势了。



```mysql
# 查看行锁状态
show status like 'innodb_row_lock%';
+-------------------------------+--------+
| Variable_name                 | Value  |
+-------------------------------+--------+
| Innodb_row_lock_current_waits | 0      | #当前等待
| Innodb_row_lock_time          | 156832 | #等待总时长
| Innodb_row_lock_time_avg      | 19604  | #等待平均时长
| Innodb_row_lock_time_max      | 51133  |
| Innodb_row_lock_waits         | 8      | #等待总次数
+-------------------------------+--------+
```



**优化建议**

- 尽可能数据检索用索引来完成，避免无索引行锁升级为表锁。

- 合理设计索引，尽量缩小锁的范围。

- 避免间隙锁

- 尽量控制事务大小，减少锁定资源量和时间长度

- 尽可能低级别事务隔离

  

### 主从复制

![WeChat5c0efd7012756a8e42403dc4d247fd8b.png](http://ww1.sinaimg.cn/mw690/008aPpVGgy1gnej0ax8zkj317s0l4b29.jpg)

MySQL复制过程分成三步：

1. master将改变记录到二进制日志(binary log)。这些记录过程叫做二进制日志事件，binary log events;
2. salve将master的binary log events拷贝到它的中继日志(relay log);
3. slave重做中继日志中的事件，将改变应用到自己的数据库中。MySQL复制是异步的且串行化的。



**复制的基本原则**

- 每个slave只有一个master
- 每个slave只能有一个唯一的服务器ID
- 每个master可以有多个slave



