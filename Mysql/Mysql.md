### 数据库的相关概念

DB（Database）

DBMS（Database Management System）

SQL（Structure Query Language）

### 服务端的登陆

```shell
mysql -h localhost -P 3306 -u root -pghj5880361.
mysql -h主机名 -P端口 -u用户名 -p密码
```

### 查看mysql版本

```mysql
mysql> select version();
+-----------+
| version() |
+-----------+
| 8.0.22    |
+-----------+
1 row in set (0.00 sec)
```

### Mysql常见命令

1. show databses;

2. 打开指定库
   use 库名

3. 查看当前库所有表
   show tables;

4. 查看其它库所有表
   show tables from 库名；

5. create table 表名（
   列名 列类型，
   列名 列类型
   ）

6. 查看表结构
   desc 表名

   

## DQL语言学习

Data Query Language

### 查询

```mysql
# 查询常量值
select 100;
select 'john'

# 查询表达式
select 100%2

# 查询函数
select version();

# 起别名
# 1.便于理解
# 2.如果要查询的字段有重名，使用别名可以区分开来
# 方式1
select 100%98 as 结果;
select last_name as 姓,first_name as 名 from employees
# 方式2
select last_name as 姓,first_name 名 from employees

# 去重
# 案例：查询员工表中涉及到的所有部门编号
select distinct department_id from employees;
# distinct 让重复的row合并成一个！

# +号的作用
/*
Java中+号：
1.运算符，两个操作数都为数值型
2.连接符，只要有一个操作数为字符串
mysql中的+号：
仅仅有一个功能：运算符
select 100+90; 两个操作数都为数值型，加法运算
sekect '123'+90; 其中一方为字符型，试图将字符型转换成数值型。
如果成功，则继续加法运算。
如果失败，则将字符型转换成0
select ‘john’+90 -> select 90;
select null+0; 只要一方为null 结果为null;
*/
# 案例：查询员工的名和姓连接成一个字段，并显示为姓名
# concat 连接字符！
select concat(last_name,first_name) 姓名 from employees;

# 某列值如果为null 赋值为0
# 这里把替换和非替换同时查进行对比
select ifnull(commission_pct,0),commission_pct from employees;

# 条件查询
/*
分类：
1.按条件表达式筛选
> < = != <> >= <=
2.按逻辑表达式筛选
&& || ! and or not
3.模糊查询
like
between and 
in 
is null
*/


```



### 