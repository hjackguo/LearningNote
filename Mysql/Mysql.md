









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

```mysql
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
```



## DQL语言学习

Data Query Language

### 基础查询

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
```

### 条件查询

```mysql
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

# 模糊查询
# like
/*
1.一般和通配符搭配使用
	%任意多个字符，包含0个字符
	_任意单个字符
*/
# 案例： 查询员工名中包含字符a的员工信息
select *
from employees
where last_name
like '%a%';
# 案例：查询员工名中第三个字符为n,第五个字符为l的员工名和工资
select last_name,salary
from employees
where last_name
like '__n_l%';
# 案例：查询员工名中第二个字符为_的员工名,\转译！
select last_name
from employees
where last_name
like '_\_%';

# between and,效果和a>b  a<c一样

/*
in 
含义：判断某字段的值是否属于in列表中某一项
特点：
	1.使用in比or提高简洁度
	2.in 列表值类型必须统一或兼容
*/
# 案例：查询员工的工种编号是IT_PROG、AD_VP、AD_PRES中的一个员工名和编号
select last_name,job_id
from employees
where job_id 
in ('IT_PROG','AD_VP','AD_PRES');
# 如果不用in 需要多个or连接

#is null
# 案例：查询没有奖金的员工名和奖金率
select last_name,commission_pct
from employees
# 没奖金
where commission_pct is null;
# 有奖金
where commission_pct is not null;
# 本处不能用 commission_pct=null，无效
# =或!=不能用于判断null值
# is null 或 is not null可以

# 安全等于  <=>
select last_name,commission_pct
from employees
# 安全等于可以判断null
where commission_pct <=> null;

# is null 和 <=> 比较
/*
is null:仅仅可以判断null,可读性高
<=>：   都可以判断，可读性低
*/

/*
经典面试题
select * from employees; 和
select * from employees where 'commission_pct' like '%%' and last_name like '%%'
结果是否一样？

答：
如果判断的字段有null值，结果不一样。
如果没有null值，则一样。
*/
```

### 排序查询

```mysql
# 语法 
select 查询列表						      3
from 表			         					1
where 筛选条件        					2
order by 排序列表 [asc|desc] 	  4
/*
1. asc升序  desc降序 如果不写  默认升序
2. order by 可以支持单个字段、多个字段、表达式、函数、别名
3. order by 子句一般放在查询语句最后面，但limit子句除外
*/

# 案例：工资高到低排序
select *
from employees
order by salary desc;

# 案例：按年薪的高低显示员工信息和年薪 [别名排序] 
select *,salary*12*(1+IFNULL(commission_pct,0)) 年薪
from employees
order by 年薪 desc # 可以直接采用上面的命名！

# 案例：按姓名长度显示员工姓名和工资 [按函数排序]
select length(last_name) 字节长度,last_name,salary
from employees
order by 字节长度 desc;

# 案例： 查询员工信息，要求先按工资升序，再按员工编号降序
select *
from employees
order by salary  asc, employee_id desc

```

### 常见函数

```mysql
/*
调用： select 函数名(参数) [from 表];
分类：
	1.单行函数
	concat、length、ifnull
	2.分组函数
	功能：统计使用，又称为统计函数、聚合函数、组函数
*/
```

#### 单行函数

```mysql

# 一、字符函数

# length 获取参数值的字节个数
select length('john'); # 4
select length('张三丰hahaha'); # 15. 一个中文占3个字节

# concat 拼接字符串
select concat(last_name,'_',first_name) from employees;

# upper、lower
select upper('john');
select lower('JOHN');
# 姓大写，名小写 拼接
select concat(upper(last_name),lower(first_name))  姓名 from employees;

# substr、 substring 
# mysql索引从1开始 
select substr('abcdefghijk',4);  # d开始 defghijk！
# 从索引4开始  后面的3个字符
select substr('abcdefghijk',4,3);  # def

# instr
select instr('abcde','cd');  # 结果为3！ b在a里的起始位置

# trim
select length(trim('  ab   ')); #2
select trim('a' from 'aaaabdeaaqewaaa'); # 'bdeaaqew' 只去前后

# lpad
select lpad('abcs',10,'*'); # ******abcs 左填充
select lpad('abcs',2,'*'); # ab 截断的话去掉右边的

# rpad
select rpad('abc',6,'df'); # abcdfd
select rpad('abc',2,'df'); # ab  截断的话也是去掉右边

# replace
select replace ('abcd','a','f'); # fbcd
```

```mysql
# 二、数学函数
select round(1.65) # 2
select round(-1.5) # -2
select round(1.567,2) # 1.57

# ceil 向上取整 返回>=该参数的最小整数
select ceil(1.00); # 1
select ceil(-1.02);# -1

# floor 向下取整 返回<=该参数的最大整数
select floor(9.99) # 9
select floor(-9.99) # -10

# truncate 截断
select truncate(1.69999,1); #1.6

# mod 取余
select mod(10,-3) # 1
select mod(-10,-3) # -1
```

```mysql
# 三、日期函数

# now 返回当前系统日期+时间
select now(); # 2021-01-01 18:54:13

# curdate 返回日期，不包含时间
select curdate(); # 2021-01-01

# curtime 返回时间，不包含日期
select curtime(); # 18:55:19

# 可以获取指定部分。年、月、日、小时、分、秒
select year(now()) 年;  # 2021
select month(now()); # 1
select monthname(now());  # January

# str_to_date
select str_to_date('1998-3-2','%Y-%c-%d'); # 1998-03-02
# data_format 日期 to 字符
select date_format(now(),'%y年%m月%d日');
```

```mysql
# 四、其他函数
select version();
select database();
select user();
```

```mysql
# 五、流程控制函数

# 1. if 函数
select if(10>5,'大','小'); # 大

select last_name,commission_pct,if(commission_pct is null,'没','有')
from employees;

# 2. case函数
/*
case 判断字段或表达式
when 常量1 then 值或语句
when 常量2 then 值或语句
else 值或语句
end
*/

/* 案例：查询员工的工资
部门号=30，显示工资1.1倍
     =40，       1.2
其他             1
*/
select salary,department_id,
case department_id
when 30 then salary*1.1
when 40 then salary*1.2
else salary
end
from employees;

/* case函数使用二：类似于多重if
when 条件1 then 要显示的值1或语句1
when 条件2 then 要显示的值1或语句2
else 要显示的值n或语句n
end
*/

/* 案例：查询员工工资情况
工资>20000显示A
工资>15000显示B
其它 C 
*/
select salary,
case
when salary>20000 then 'A'
when salary>15000 then 'B'
else 'C'
end
from employees;
```

#### 分组函数

``` mysql
/*
分类：
sum 、 avg、 max、 min、 count
全部忽略null
可以和distinct 搭配实现去重
*/
select sum(salary)from employees;
select avg(salary)from employees;
select max(salary)from employees;
select min(salary)from employees;
select count(salary)from employees;

# 非重复值sum
select sum(distinct salary) from employees;

# count函数详细介绍
select count(*) from employees;
select count(1) from employees;
/* 效率：
MYISAM 存储引擎下, COUNT(*)效率高
INNODB 存储引擎下, 效率差不多
*/
```

### 分组查询

```mysql
/*
select 分组函数,列（出现在group by后面)
from 表
where  
group by 分组的列表
order by

注意：
		查询列表特殊，要求是group by后出现的字段
		
特点：
	1、分组查询中的筛选分为两类
	分组前筛选  原始表 				where(group by 前)
	分组后筛选  分组后结果集		having(group by 后)
	
	分组函数做条件肯定放在having子句中
	能用分组前筛选优先考虑分组前筛选
	
	2、group by子句支持单个字段分组，多个字段分组，表达式或函数
	3、也可以添加排序（放在分组查询最后）
*/


# 案例:查询每个工种最高工资
select max(salary),job_id
from employees
group by job_id;

# 案例：查询每个位置上的部门个数
select count(*),location_id
from departments
group by location_id;

# 案例：邮箱中包含a的，每个部门的平均工资
select avg(salary),department_id
from employees
where email like '%a%'
group by department_id;

# 案例：查询有奖金的每个领导手下员工最高工资
select max(salary),manager_id,
from employees
where commission_pct is not null
group by manager_id;


# 复杂筛选条件(分组后的筛选结果)

# 案例：哪个部门员工个数>2
#1.查询每个部门员工个数
select count(*),department_id
from employees
group by department_id;
#2. 根据1的结果，查询个数大于2
select count(*),department_id
from employees
group by department_id
having count(*)>2;

# 案例： 查询每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
select job_id,max(salary)
from employees
where commission_pct is not null
group by job_id
having max(salary)>12000; 

# 案例：领导编号>102的每个领导手下员工的最低工资>5000的领导编号是哪个，以及最低工资
select manager_id,min(salary)
from employees
where manager_id>102
group by manager_id
having min(salary)>5000;

# 按表达式或函数分组
# 案例：按员工姓名长度分组，查询每一组员工个数，筛选员工个数>5的
select length(last_name),count(*)
from employees
group by length(last_name)
having count(*) > 5;
# 别名
select length(last_name) len_l,count(*) c
from employees
group by len_l
having c > 5;

# 按多个字段分组
# 案例：查询每个部门每个工种的员工的平均工资
select avg(salary),department_id,job_id
from employees
group by department_id,job_id;

# 添加排序
# 案例：查询每个部(部门不为null)，每个工种的员工的平均工资,高于10000的按高->低排序
select avg(salary),department_id,job_id
from employees
where department_id is not null
group by department_id,job_id
having avg(salary)>10000
order by avg(salary) desc;
```

### 连接查询

```mysql
/*
笛卡尔乘积现象：  表1 m行， 表2 n行， 结果=m*n行
发生原因： 没有有效的连接条件

分类：
		按年代分类：
		sql92标准：仅仅支持内连接
		sql99标准【推荐】：支持内连接+外连接（左外和右外）+交叉连接
		
		按功能分类：
				内连接：
						等值连接
						非等值连接
						自连接
        外连接：
        		左外连接
        		右外连接
        		全外连接
       	交叉连接
*/

# 一、sql92标准


```

#### sql92(内连接)

##### 等值连接

```mysql
# 1、等值连接
/*
1、 多表等值连接结果为多表交集部分
2、 n表连接，至少需要n-1个连接条件
3、多表顺序没要求
4、一般需要为表取别名
5、可以搭配所有子句使用
*/
# 案例：查询女生名和男生名
select name,boyName
from boys,beauty
where boyfriend_id = boys.id;
# 案例：查询部门名和对应的部门名
select last_name,department_name
from departments,employees
where employees.department_id = departments.department_id;

# 为表其别名
/*
1、提高语句简洁度
2、区分多个重名字段
注意： 如果为表取了别名，则查询字段不能使用原来的表名去限定
*/
# 案例： 查询员工名、工种号、工种名
select last_name,e.job_id,job_title
from jobs j,employees e
where e.job_id = j.job_id;
```

##### 非等值连接

```mysql
# 案例：查询员工工资和工资级别
select salary,grade_level
from employees,job_grades
where salary between lowest_sal and highest_sal;
```

##### 自连接

```mysql
# 案例：员工名和上级的名称
select e.last_name employee,m.last_name manager
from employees e,employees m
where e.manager_id=m.employee_id;
```

#### sql99

```mysql
/*
语法：
select 查询列表
from 表1 别名 【连接类型】
join 表2 别名
on 连接条件
【where 筛选条件】
【group by】
【having】
【order by】

内接连     ：inner
外连接
		左外  ：left [outer]
		右外  ：right[outer]
		全外  ：full [outer]
交叉连接	 ：cross
*/
```

##### 内连接

```mysql
/*
select 
from 表1
inner join 表2
on 连接条件

分类：
等值
非等值
自连接

特点：
1.添加排序、分组、筛选
2.inner可以省略
3.筛选条件放在where后面，连接条件放在 on 后面，提高分离性，便于阅读
4.inner join和sql92语法的等值连接一样
*/

# 等值连接
# 案例：查询员工名，部门名
select last_name,department_name
from employees e
inner join departments d
on e.department_id=d.department_id;
# 案例：查询名字中包含e的员工名和工种名
select last_name,job_title
from employees e
inner join jobs j
on e.job_id=j.job_id
where last_name like '%e%';
# 案例：查询部门个数>3的城市名和部门个数
select city,count(*)
from departments d
inner join locations l
on d.location_id=l.location_id
group by city
having count(*)>3;
# 案例：查询员工名、部门名、工种名，并按部门名降序
select last_name,department_name,job_title
from employees e
inner join departments d on e.department_id=d.department_id
inner join jobs j on e.job_id=j.job_id
order by department_name desc;

# 非等值连接
# 案例：查询员工工资和工资级别
select salary,grade_level
from employees
inner join job_grades
where salary between lowest_sal and highest_sal;

# 自连接
# 案例：员工名和上级的名称
select e.last_name employee,m.last_name manager
from employees e
inner join employees m
where e.manager_id=m.employee_id;

```

##### 外连接

```mysql
/*
应用场景：一个表中有，另一个表中没有

特点：
1、外连接的查询结果为主表中所有记录
		如果从表中有和它匹配的，则显示匹配的
		如果从表中没有匹配的，则显示null
		外连接查询结果=内连接结果+主表中有而从表中没有的记录
2、左外连接：left join 左边是主表
	 右外连接：right join 右边是主表
3、左外和右外交换两个表的顺序，可以实现一样效果
4、全外连接=内连接的结果+表一独有+表二独有
*/

# 引入：查询男朋友不在男生表的女生名
# 左外连接
select b.name,bo.*
from beauty b
left outer join boys bo
on b.boyfriend_id=bo.id
where bo.id is null;
# 案例：查询哪个部门没有员工
select d.*,employee_id
from departments d
left outer join employees e
on d.department_id=e.department_id
where employee_id is null;

# 全外连接 （两个表各自没有的部分都能查出来,mysql不支持）
full outer join 
```

##### 交叉连接（笛卡尔乘积）

```mysql
select b.*,bo.*    # beauty表 12行   boys 4行
from beauty b,
cross join boys bo; # 结果 48行
```

#### sql92和sql99pk

功能：sql99支持的较多

可读性：sql99实现连接条件和筛选条件分离，可读性高



------



### 子查询

出现在其它语句中的select语句，称为子查询或内查询。

外部的查询语句，称为主查询或外查询。

分类：

按子查询出现的位置：

​					select 后面：

​							仅支持标量子查询

​					from 后面：

​							支持表子查询

​					* where或having后面 ：

​							*标量子查询（单行）

​							*列子查询 （多行）

​							行子查询

​					exists后面（相关子查询）

​							表子查询

按结果集的行列不同：

​					标量子查询（结果集有一行一列）

​					列子查询（有一列多行）

​					行子查询（一行多列）

​					表子查询（结果集一般为多行多列）



#### where或having后面

1、标量子查询（单行子查询）

2、列子查询

3、行子查询（一行多列）



特点：

1.子查询在小括号内

2.子查询一般方条件右边

3.标量子查询，一般用单行操作

< >= <= = < >

列子查询一般搭配多行操作符

in 、any/some、 all

4.子查询的执行优先于主查询

```mysql
# 标量子查询
# 案例：查询谁的工资比Abel高？
# 1.查询Abel工资
select salary
from employees
where last_name="Abel";
# 2.查询员工信息，满足>1
select * 
from employees
where salary > (
    select salary 
    from employees
    where last_name="Abel"
);

# 案例： 返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资
#1. 查141员工job id
select job_id
from employees
where employee_id=141
# 2. 查143号员工salary
select salary 
from employees
where employee_id = 143
# 3.查询员工姓名，job_id和工资 要去job_id=1并且salary>2
select last_name,job_id,salary
from employees
where job_id=(
    select job_id
    from employees
    where employee_id=141
) and salary>(
    select salary 
    from employees
    where employee_id = 143
)

# 案例:返回公司工资最少的员工的last_name,job_id和salary
#1. 查询工资最少的
select min(salary)
from employees
#2.
select last_name,job_id,salary
from employees
where salary=(
    select min(salary)
    from employees
);

# 案例：查询最低工资大于50号部门最低工资的部门id和其最低工资
# 1.50号部门最低工资
select min(salary)
from employees
where department_id = 50
# 2. 查询每个部门最低工资
select department_id, min(salary)
from employees
group by department_id
having min(salary)>(
    select min(salary)
    from employees
    where department_id = 50
);
```

```mysql
# 列子查询（多行子查询）
/**
操作符
in/not in
any|some
all
**/

# 案例：返回location_id是1400或1700的部门中所有员工姓名
select last_name
from employees
where department_id in(
    select department_id
    from departments
    where location_id in (1400,1700)
);

select last_name
from employees
where department_id = any(
    select department_id
    from departments
    where location_id in (1400,1700)
);

# 案例：返回其它工种中比job_id为'IT_PROG'部门任一工资低的员工的工号、姓名、job_id以及salary
select employee_id,last_name,job_id,salary
from employees
where salary<any(
    select salary
    from employees
    where job_id='IT_PROG'
) and job_id!='IT_PROG';

# 案例:返回其它工种中比job_id为'IT_PROG'部门所有工资低的员工的工号、姓名、job_id以及salary
select employee_id,last_name,job_id,salary
from employees
where salary<all(
    select salary
    from employees
    where job_id='IT_PROG'
) and job_id!='IT_PROG';
```

```mysql
# 行子查询（1行多列、多行多列）

# 案例： 查询员工编号最小，工资最高的员工信息
# 1.查询最小的员工编号
select min(employee_id)
from employees
# 2.查询工资最高
select max(salary)
from employees
# 3.
select *
from employees
where employee_id=(
  select min(employee_id)
	from employees
) and salary = (
  select max(salary)
	from employees
);

select *
from employees
where (employee_id,salary)=(
  select min(employee_id),max(salary)
	from employees
);
```

#### select 后面

```mysql
# 案例：查询每个部门员工个数
select d.*,(
  select count(*)
  from employees e
  where e.department_id = d.department_id
)
from departments d;

# 案例：查询员工号=102的部门名
select (
  select department_name
  from departments d
  inner join employees e
  on d.department_id = e.department_id
  where e.employee_id=102
);

```

#### from后面

```mysql
# from后面

#案例：查询每个部门的平均工资的工资等级
select avg(slary),department_id 
from employees
group by department_id

select * from job_grade;

# 2连接1的结果集和job_grade表，筛选条件
select ag_dep.*,g.grade_level
from (
  select avg(slary),department_id 
	from employees
	group by department_id
) ag_dep
inner join job_grades g
on ag_dep.ag between lowest_sal and highest_sal;

```

### 分页查询

```mysql
/**
应用场景：当要现实的数据，一页显示不全，需要分页提交sql请求

语法：
		select 
		from
		join type
		on
		where
		group by
		having
		order by
		limit offset,size; # 放在最后，执行也是在最后
		
		offset 要显示条目的起始索引(从0开始)
		size 要现实的条目个数
		
特点：
   1. limit语句放在查询最后
   2. 公式
   要显示的页数page,页的条数size
   
   select 
   from
   limit (page-1)*size,size;
**/
# 案例：查询前五条员工信息
select *
from employees
limit 0,5;

select *
from employees
limit 5; # 0被省略

# 案例：有奖金员工新奇，并且工资高的前10名
select *
from employees
where commission_pct is not null
order by salary desc
limit 10;
```

### 联合查询

```mysql
/**
union 联合；将多条查询语句合并成一个结果

语法：
查询语句1
union
查询语句2
union
...

应用场景：
要查询的结果来自于多个表，多个表没有直接的连接关系，但查询的信息一致时。

特点：
1. 要求多条查询语句查询列数一致
2. 要求多个查询语句的查询的每一列的类型和顺序最好一致
3. union默认去重，union all 允许重复。
**/

# 案例：查询部门编号>90或者邮箱包含a的员工信息
select *
from employees
where email like '%a%'
or department_id>90;

select *
from employees
where email like '%a%'
union 
select *
from employees
and department_id>90;
```

## DDL语言

### 常见约束

```mysql
/*
分类： 六大约束

	NOT NULL: 非空，用于保证字段不为空
	DEFAULT：默认值
	PRIMARY KEY：主键，用于保证字段唯一性，并且非空
	UNIQUE： 唯一值，可以为空
	CHECK：检查约束【mysql不支持】
	FOREIGN KEY: 外键，用于限制两个表的关系，用于保证该字段必须来自于主表的关联列的值。(在从表添加外键约束，用于引用主表中某列的值)
	
添加约束时机：
	1.创建表时
	2.修改表时
	
约束的添加分类：
	列级约束
	表级约束
*/
create table 表名{
		字段名 字段类型 列级约束，
    字段名 字段类型 列级约束，
		表级约束
}
```

```mysql
# 创建列级约束

create table stuinfo(
		id int primary key,   #主键
  	stuName varchar(20) not null,  # 非空
		gender char(1) check(gender='男' or gender='女'), # 检查
    sear int unique, # 唯一
    age int default 18,  #默认
    marjorid int foreign key references major(id) # 外键（不支持列级，但不报错）
);

create table major(
		id int primary key,
    majorName varchar(20)
);

# 查看stuinfo表中所有索引，包括主键、外键、唯一
show index from stuinfo;


# 表级约束
/*
constraint 约束名(可忽略) 约束类型(字段名)
*/
create table stuinfo(
	id int,  
  	stuName varchar(20), 
	gender char(1),
    seat int ,
    age int,
    majorid int,
    
    constraint pk primary key (id),
    constraint uq unique(seat),
    constraint ck check(gender='男' or gender='女'),
    constraint fk foreign key(majorid) references major(id)
);

# 通用写法
create if exists table stuinfo(
		id int primary key,
  	stuName varchar(20) not null, 
		gender char(1),
    sear int unique, 
    age int default 18, 
    marjorid int,
  	constraint fk_stuinfo_major foreign key(majorid) references major(id)
);
```

```mysql
# 外键
/*
1. 要求在从表设置外键关系
2. 从表的外键列的类型和主表关联列类型要一致或兼容，名称无要求
3.主表的关联列必须是一个key（一般是主键或唯一）
4.插入数据，先插入主表，再插入从表
删除数据时，先删除从表，再删除主表
*/
```

```mysql
# 标示列

/*
自增长列

特点：
1. 必须和主键搭配吗？ 不一定，但要求一定是key.
2.一个表可以有几个标识列 ？ 至多一个
3.只能数值型
4.set auto_increment_increment=3，设置步长
手动插入值设置起始值
*/

# 创建表时设置标识列
create table tab_identity(
	id int primary key auto_increment,
  name varchar(20)
);
```

## TCL语言

```mysql
/**
Transaction Control Language 事务控制语言

事务：
一个或者一组，要么全执行，要么全不执行。

innodb支持事务，myisam、memory不支持。

事务的ACID属性
1.原子性(Atomicity)
事务是一个不可分割的工作单位，要么都发生，要么都不发生。
2.一致性（Consistency）
事务必须从一个一致状态变换到另一个一致状态。 没有中间过程！
3.隔离型（Isolation）
一个事务的执行，不受别的事务的干扰。
4.持久性（Durability）
事务一旦提交，他对数据库的改变是永久性的。
*/

show engines;
```

### 事务的创建

```mysql
/*
隐式事务：事务没有明显的开启和结束的标记
比如 insert,update,delete
*/

/*
显示事务：事务具有明显的开启和结束标记
前提：必须先设置自动提交功能为禁用

步骤1:开启事务
set autocommit=0;
start transaction; (可选)
步骤2:编写事务中sql
语句1;
语句2;
...
步骤3:结束事务
commit; 提交事务
rollback; 回滚事务

savepoint 节点名；设置保存点
*/
set autocommit = 0;

# 演示事务

# 开启事务
set autocommit=0;
start transaction;
# 编写事务
update account set balance = balance - 500 where username='张无忌';
update account set balance = balance + 500 where username='赵敏';
# 结束事务
commit;
rollback;


```

savepoint 使用

```mysql
set autocommit = 0;
delete from account where id=25;
savepoint a;
delete from account where id=28;
rollback to a;
```



### 数据库隔离级别

```mysql
/*
多个事务访问数据库中相同数据带来问题：
1.脏读：T1读了已经被T2修改但没提交字段，T2回滚，T1读取的数据就是临时且无效的
2.不可重复读：T1读取了一个字段，T2更新了该字段。之后T1再次读取同一个字段，值就不同了。
3.幻读：T1从一个表读了一个字段，T2在该表插入行的一行后，如果T1再次读这个表，会多出几行。

数据库4种事务隔离级别
READ UNCOMMITTED
允许事务读取未被其它事务提交的变更。
脏读、不可重复读和幻读都会出现

READ COMMITED
只允许事务读取已经被其它事务提交的变更。
可以避免脏读
但不可重复读和幻读问题仍然出现

REPEATABLE READ
确保事务可以多次从一个字段种读取相同的值。在这个事务持续期间，禁止其他事务对这个字段进行更新。
可以避免脏读和不可重复读
但幻读的问题仍然存在

SERIALIZABLE （串行化）
确保事务可以从一个表读相同行，在这个事务执行期间，禁止其它事务对表执行插入，更新和删除。
所有并发问题都可以避免，性能十分低下

									脏读  不可重复读   幻读
read uncommited		 Y			Y					Y
read committed			N			Y					Y	
repeatable read			N			N					Y	
serializable				N			N					N

mysql默认repeatable read

查看隔离级别
select @@transaction_isolation;

设置隔离级别
set session transaction isolation level repeatable read;
*/

```

