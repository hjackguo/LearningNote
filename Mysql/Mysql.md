





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



### 子查询

```

```

