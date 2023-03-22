---
title: mysql基础
date: 2023-02-00 21:00:00
tags: 数据库
categories: java
---

### 安装与删除

> 通过exe 文件安装
>
> 卸载时再点击安装程序，选择 remove 即可卸载。卸载后还需删除两个地方文件。
>
> - C:/ProgrameData/mySQL
> - C:/program Files(x86)/mySQL
>
> 如果不删除干净，下次会安装不上

### sql,DB,DBMS分别是什么？啥关系？

- DB: 

	DataBase (数据库，数据库实际再电脑上以文件形式存在)

- DBMS： 

	DataBase Management System(数据库管理系统，常见 MYSQL，Oracle，DB2,SqlServer...)

- SQL: 

	结构化查询语句，是一门标准化通用语言。标准的sql适用于所有数据库产品

关系： DBMS 执行 SQL 操作 DB

<!--more-->

### 什么是表？

> table 是数据库的基本组成单元，所欲数据仪表的形式组织，目的时可读性强
>
> 一个表包括行，列
>
> - 行： 数据
> - 列：字段 【字段包括 字段名，数据类型，相关约束】



### SQL 语句分类

- DQL：【查询】查询语句 select
- DML：【操作】insert delete update 表数据增删改
- DDL：【定义】create drop alter，表结构增删发
- TCL： 【事务控制】commit，rollback 事务
- DCL： 【数据控制】grant授权，revoke撤销权限

<!--more-->

### DOS窗口命令

- 登录MySQL

	```
	mysql -uroot -p123
	```

- 数据库操作

	命令结束最好带; 号，否则可能判断语句未结束，不执行

	```java
	// 查看数据库
	show databases
	    
	// 创建数据库
	create database xxx
	    
	// 删除数据库
	drop database xxx
	    
	// 使用数据库
	use xxx
	    
	// 查看表
	show tables
	    
	// 查看表结构
	desc xxxprint
	    
	// 查看创建表的语句
	show create table emp
	    
	// 查看使用的是哪一个数据库
	select database()
	    
	// 查看版本
	select version()
	    
	// 初始化数据,执行外部sql 脚本文件创建表
	source D/xxxxx.sql
	```

### 查询

#### 简单查询（DQL）

```java
// 注意，分号结尾，不区分大小写
select 字段名1,字段名2,字段名3 ... from 表名;

// 查询列别名, as 关键字可以省略
select ename,sal * 12 as '年薪' from emp;
```

#### 单表条件查询

![image-20230202222207356](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230202222207356.png)

```java
select 字段... 5
    from 表名 1
    where 条件 2
    group by 条件 3
    having 条件 4
    order by 关键字 6
    limit ; 7

// 排序 asc 升序,desc 降序
    // 查询 ename 字段 从 emp 表 排序 以 sal 字段 升序  sal1 降序 
    // z只有前面字段无法排序时才会启用后面字段
select ename from emp order by sal asc, sal1 desc;
```

##### 分组函数

**不可直接使用在where 语句**，因为 group by 实在where 执行之后才会执行

**分组函数在group by 之后执行**

| 函数名 | 说明                   |
| ------ | ---------------------- |
| count  | 取得记录               |
| sum    | 求和，自动忽略null数据 |
| avg    | 取平均值               |
| max    | 取最大值               |

```sql
// 对某字段求和
select sum(sal) from emp;
// 统计
select count(*) from emp;
select count(ename) from emp;

/*
	所有数据库规定，只要与null 参与的运算结果一定是null.
	使用 ifnull函数
	ifnull(可能时null的字段, 被当作什么处理)
*/
select enamr, ifnull(comn, 0) as comm from emp;

```

##### **count * 与 count 某个字段的区别？**

> count * 统计所欲条数， count 字段名统计该字段不为空的条数

##### **group by 和 having**

> **group by  后 才能使用 having
>
> group by: 按照某个字段或者某些字段进行分组
>
> having： 对分组之后的数据经行筛选

```sql
// 找出每个工作岗位的最高工资
select max(sal) from emp group by job;
```

```sql
// 找出工资高于平均工资的员工
select ename from emp where sal > (select avg(sal) from emp);
```

##### **having 和 group by 如何选择？**

eg: 找出每个部门最高薪资，要求显示薪资大于2900的数据

```sql
// 找出每个部门最大薪资、
select max(sal) ,deptno from emp by deptno;
// 找出薪资大于 2900 【效率低】
select max(sal) ,deptno from emp by deptno having max(sal) > 2900;

// 最佳方案
select  max(sal) ,deptno from emp where sal > 2900 by deptno;
```

**但是，当where 条件满足不了？**

```sql
// 要求平均薪资大于2000
select  max(sal) ,deptno from emp where sal > avg(sal) by deptno; // 错误，where 后面不能用分组

此时只能用 having
```



##### **多个字段联合起来一起分组？**

> 将多个字段拼在一起看成一个字段

eg: 找出每个部门不同工作岗位的最高薪资

```sql
select deptno,jop,max(sal)
    from emp
    group by deptno, job;
```

#### 关于查询结果的去重

> dsitinc： 修饰查询的字段名，只能出现在所有字段的最前面

```sql
// 表示所有字段联合去重
select distinc deptno,jop from emp;
```



#### 连接查询

> 实际开发中，大部分业务都不是单表查询数据发，一般都是夺标联合查询取最终结果

##### 连接查询的分类？

> 根据语法划分：
>
> SQL92（老DBA在使用）
>
> SQL99（比较新）

根据连接方式划分，包括：

- 内连接

	> 假设A和B表经行连接，使用内连接的话，凡是A表和B表能匹配的上的记录查询出来，这就是内连接。两张表平级，没有主副

	

	- 等值连接
	- 非等值连接
	- 自连接

- 外连接

	> 假设A和B表进行连接，使用外连接的话。一张是主表，主要查询主表中的数据捎带查询附表中的数据，当副表中没有数据和主表中的数据匹配上，副表自动模拟出NULL与之匹配

	

	- 左外连接（左连接，左边是主表）
	- 右外连接（右连接，右边是主表）

- 全连接（很少用）



##### 笛卡儿积现象

> 如果两张表连接查询没有加限制条件，则结果是两张表记录的条数乘积

```sql
// 查询所有员工的部门名称
select e.ename,d.dname from emp e,dept d;
```

##### 等值连接

> 条件是等量关系

```sql
// 查询每个员工的部门名称，要求显示员工名和部门名

SQL92:
select e.ename ,d.dname
from emp e, dept d
where e.deptno = d.deptno;

SQL99:(常用)
select e.ename ,d.dname
from emp e
join dept d
on e.deptno = d.deptno;

内连接语法：inner join 简写 join
...
	A
join B
on 
	连接条件
where
	...
```



##### 非等值连接

> 连接条件是非等值关系

```sql
// 找出每个员工的工资等级，要求显示员工名称，工资，工资等级

select e.namme, e.sal, s.lv
from emp e
join salgrade s
on e.sal vetween s.losal and s.hisal;// no 条件不是等量关系

```

##### 自连接

> 一张表看作两张比表,自己连接自己

```sql
// 找出每个员工的上级领导，要求显示员工的姓名和对应领导名字。员工表中有领导和员工 mar 字段 存储了员工的编号

select e.ename as '员工名', m.ename as '领导名'
from emp e
join emp m
on e.mar == m.empno;

// 内连接查询大Boss 查不出来
```



##### 外连接

```sql
select e.ename as '员工名', m.ename as '领导名'
from emp e
left join emp m
on e.mar == m.empno;

// 外连接可以查出大Boss 数据 对应领导 NULL
```

```sql
// 查询哪个部门没有人
select d.*
from dmp e
right join dep d
on e.deptno = d.deptno
where e.ename is null;
```

##### 三张表以上的表连接查询

```sql
// 找出每一个员工的部门名称以及工资情况

select e.name, d.name, s.grade, e1.enmae '领导'
from emp e
join dept d
on e.deptno = d.deptno
join salgrade s
on e.sal between s.losal and s.hisal
left join emp e1
on e.mgr = e1.empno;

// 先查A + B，在查A + C
...
A
join B
join C
...
```

#### 子嵌套查询

> select 语句中嵌套select语句

```sql
// 找出高于平均薪资的员工信息

select * from emp where sal > (select avg(sal) from emp)
// 找出每个部门平均薪资等级
// 1、找出每个部门平均薪水（按照部门编号分组，求sal）
select e.deptno ,avg(sal) from emp group by deptno;

// 2、将上一步的查询结果当作临时表t, 让t和表 salgrade 连接，条件是: t.avgsal between s.losal and s.hisal

```

```sql
// 找出所有员工的部门名称
select e.ename (
	select d.ename from dept d where e.deptno = d.deptno
) sd dname
from emp;
```

#### union 

> 将查询的结果集相加, 可以是两张不相干的表数据查询结果整合。
>
> 但是两个表查询结果数量一致时才能整合

```sql
// 找出公估工作岗位是 SALEMA和MANAGER的员工
select ename,job 
from emp
where job = 'SALEMA' or 'MANAGER'
where jon in ('SALEMA' ,'MANAGER')

select ename,job from emp where job = 'SALEMA'
union
select ename,job from emp where job = 'MANAGER'
```

#### limit

> limit 是 MySQL 特有的，其他数据库么有
>
> limit 起始位置，条数

```sql
select * from emp limit 0,10
// 第一页 pageSise = 10
0, pageSise
// 下一页, n代表页数 1 开始
pageSise*(n - 1), pageSise
```



### 创建表及操作

建表语句语法格式：

```sql
create table 表名 (
    字段1 数据类型,
    字段2 数据类型,
    字段3 数据类型,
    ...
)
```

##### 常见的数据类型

- int 整数型
- bight 长整型
- float 浮点型
- char 定长字符串
- date 日期类型
- BLOB 二进制大对象（图片视频流媒体信息）
- CLOB 字符大对象 (储存较大文本，4G字符串)

##### insert 语句插入数据

> 语法格式：
>
> insert into 表明 （字字段2,字段2,段3,..） values (值1, 值2, 值3);
>
> 字段和值数量相同	

```sql
insert into t_student
(name,class,birth)
values
('张三', 3, '2006-1-1'),
('李四', 3, '2006-1-2');
```

##### 表的复制

> 语法：将后边查询的内容插入到 dept1表
>
> ```sql
> insert into dept1 select * from dept
> ```
>
> 

##### 修改数据

> 语法格式：
>
> update 表名 set 字段1=值1,字段名2=值2....where...
>
> 

```sql
update t_student set name = '王五' where 条件
```

##### 删除数据

> 语法格式：
>
> delete from 表名 where 条件;
>
> **没有条件全部删除**

```sql
delete from dept1 where no = 10;
```

##### **删除大表**

> delete 删除不清空内存，可以回滚操作。删除效率慢，尤其是大数据表
>
> ```sql
> // 表的截断，删除快，不可回滚
> truncate table emp1;
> ```

##### 表结构的修改

> 通过 工具 来操作



#### 约束

> 为了保证数据合法性，完整性

- 非空约束 （not null）字段不能为空
- 唯一约束 （unique）字段不能重复
- 主键约束 （primary key）既不能为空也不能为null，也不能重复
- 外键约束 （foreign key）
- 检查约束 （check）MySQL不支持

使用

```sql
create table 表名 (
    字段1 数据类型 not null,// 列级约束
    字段2 数据类型 ,
    字段3 数据类型 ,
    unique(字段1,字段2)// 多个字段联合起来添加约束unique【表级约束】
)
```

##### 主键自增

```sql
drop table t_class if exists t_class;
create table t_class (
    id int primary key auto_increment,// 列级约束
    name varchar ,
    address varchar ,
    ...
    
)
 
```

##### 外键约束

> 表示这个字段引用其 它表的某个字段，并且只能是 其他表的某个字段 中有的值

```sql
create table t_class(
    cno int,
    cname verchar(255),
    primary key(cno)
);
// 被引用的字段要拥有唯一性，一般都是引用主键
create table t_studenet(
    cno int,
    sname verchar(255),
    classno int,
    foreign key(classno) reference t_class(c_o)
);

```



### 存储引擎

> 表默认的存储引擎是 InnoDB
>
> show create table 表名，可见
>
> 存储引擎这个名字只有在MySQL中存在(理解为存储方式)，每一个存储引擎都有优缺点
>
> 查看当前 MySQL 支持的存储引擎
>
> ```sql
> show engines \G
> -----------------------------------
> Engine: InnoDB
>      Support: DEFAULT
>      Comment: Supports transactions, row-level locking, and foreign keys
> Transactions: YES
>           XA: YES
>   Savepoints: YES
> // 支持事务，外键，行级锁，外键。安全得到保障。SQL服务器崩溃后提供自动恢复
> ------------------------------------
> MyISAM
> 
> 优点：
> 可悲压缩，节省储存空间。并且可以转换为只读表，提高检索效率
> 缺点：不持支事务
> -------------------------------------
> MEMORY 存储引擎
> -表数据及索引存储在内存中，查询贼快。数据易丢失
> 
> ```

### 事务

> 一个事务是一个完整的业务逻辑单元，不可再分
>
> 比如：银行账户转账，从A账户向B账户转账1000，需要
>
> A账户减1000，B账户加1000
>
> **一条语句可以搞定的事不需要事务**

**和事务相关的语句：DML语句（insert delete update）**

![image-20230205153456211](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230205153456211.png)

##### 四大特性

> ACID

- A：原子性：事务最小的单元，不可拆分
- B：一致性：事务必须保证多条DML语句同时成功或同时失败
- Ｃ：隔离性：事务A与事务B之间具有隔离
- D：持久性：持久性事最终数据必须持久化到硬盘文件中，事务才算结束

#### 关于事务之间的隔离性

事务隔离性存在隔离级别，理论上隔离级别包括4个:

- 第一级别: 读未提交 (read uncommitted)对方事务还没有提交，我们当前事务可以读取到对方未提交的数据读未提交存在脏读 (Dirty Read)现象:表示读到了脏的数据。

- 第二级别: 试已提本 (read committed)
	对方事务提交之后的数据我方可以读取到。这种隔离级别解决了: 脏读现象没有了。读已提交存在的问题是:不可重复读。

- 第三级别:可重复读 (repeatable read)
	这种隔离级别解决了:不可重复读问题。这种隔离级别存在的问题是:读取到的数据是幻象。

- 第四级别: 序列化读/串行化读解决了所有问题。效率低。需要事务排队。

> MySQL事务默认情况下是自动提交的，任意DML语句执行完就提交一次
>
> 关闭自动提交：
>
> ```sql
> // 开启事务
> start　transaction;
> 
> // 事务回滚，
> rollback;
> 
> // 提交事务
> commit;
> 
> // 设置事务级别
> set global transation isolation level read uncommitted;
> 
> // 查看全局事务隔离级别
> select @@global.tx_isolation;
> 
> ```



### 索引

> 索引相当于一本书的目录，通过目录可以快速找到对应的资源。
>
> 数据库中查询一张表有两种检索方式：
>
> 1. 全表扫描
>
> 2. 根据索引检索（效率高）、
>
> 	缩小了检索范围

##### 索引虽然可以提高检索效率，但是不能随意的添加索引，因为索引也是数据库当中的对象，也需要数据库不断的维护。是有维护成本的。比如，表中的数据经常被修改这样就不适合添加索引，因为数据一旦修改，索引需要重新排序，进行维护。

##### 什么时候考虑给字段添加索引？

- 数据量庞大
- 字段很少的DML操作，（字段修改，索引也需要维护）
- 该字段经常出现在where字句中

> 主键和具有unique约束的字段会自动添加索引

##### 查看sql语句的执行计划

```sql
explain select ename,sal from emp where sal = 1000;
```



##### 添加删除索引

```sql
// 添加索引
create inedx emp_ename_index on emp(ename);

// 删除
drop inedx emp_ename_index;
```

##### 索引实现原理

> 通过B Tree缩小扫描范围，底层索引进行了排序，分区，索引会携带数据在表中的物理地址”最终通过索引检索到数据之后，获取到关联的物理地址，通过物理地址定位表中的数据，效率是最高的。
>
> ```sql
> select ename from emp where ename = 'SMITH' :通过索引转换为:
> select ename from emp where 物理地址 = 0x3;
> ```
>
> 

![image-20230205162205142](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230205162205142.png)

##### 索引的分类

- 单一索引：给单个字段添加索引
- 复合索引：给多个字段联合添加1个索引
- 主键索引
- 唯一索引

**索引的失效**

> ```sql
> select enamefrom emp where ename like '%A%';
> ```
>
> 模糊查询的时候，第一个通配符使用的是%，这个时候索引是失效的。



### 视图（view）

> 站在不同角度去看到数据
>
> 对视图进行增删改查，会影响到原表数据

#### **视图的作用？**

> 可以隐藏数据库真实字段名等实现细节。保密级别高的系统，数据库只提供相关的视图，Java程序员只对视图操作

```sql
// 创建视图, 只有查询语句可以创建视图
create view myview as select empno,ename from emp;
// 删除视图
drop myview myview;
// 通过视图修改原表数据。
update myviewl set ename='hehe',sal=1 where empno = 7369;
// 通过视图删除原表数据。
delete from,myview1 where empno = 7369;
```



### DBA命令

- 新建用户

- 授权

- 回收权限

- 导入导出

	```sql
	// wondows dos 命令
	
	// 导出库
	mysqldump 库名>D:\xxx.sql(导出的路径) -u用户名 -p密码
	
	// 导出指定表
	
	mysqldump 库名 表名>D:\xxx.sql -u用户名 -p密码
	
	// 导入库
	create database 库名
	use 库名
	source sql文件路径
	```



### 数据库设计三范式

> 三范式设计，不会产生数据冗余

- 第一范式:任何一张表都应该**有主键**，并且每一个**字段原子性不可再分**

- 第二范式:建立在第一范式的基础之上，所有非主键字段完全依赖主键，**不能产生部分依赖**。

- 建立在第二范式的基础之上，所有非主键字段直接依赖主键，**不能产生传递依赖**。

	

> 实际开发中，以实现用户需求为主，有时候那冗余换执行速度

> 一对一设计：
>
> 主键共享、外键唯一
>
> 一对多设计：
>
> 两张表，多的表加外键
>
> 多对多设计：
>
> 三张表，关系表两个外键。