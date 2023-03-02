---
title: jdbc基础
date: 2023-02-10 21:00:00
tags: jdbc基础
categories: java
---



> JDBC 是SUN公司制定的一套接口

### JDBC 准备工作，加载jar 驱动包，然后配置到环境变量 classpath 当中

```java
classpath=.;xxx/mysql-connector-java-5.1.23-bin.jar
```

> 其中配置了两个路径，一个是当前路径，一个是mysql驱动架包路径。这里的配置在集成工具中不需要配

### jdbc 编程6步

1. 注册驱动　（连接哪个数据库）

	```java
	Driver driver = new com.mysql.jdbc.Driver();
	DriverManager.registerDriver(driver);
	
	// 方式二, 应为参数是字符串可以提出配置，类加载时自动执行静态代码 自动注册
	Class.forName("com.mysql.jdbc.Driver")
	```

	

2. 获取连接　（JVM进程与数据库连接，重量级通信）

	```java
	String url = "";
	String user = "";
	String pwd = "";
	Connection coon = DriverManager.getConnection(url, user, pwd);
	System.out.println("数据库连接对象" + coon);
	```

	

3. 获取数据库操作对象（专门执行SQL）

	```java
	Statement stmt = coon.createStatement();
	```

	

4. 执行SQL语句

	```java
	String sql = "delete * from tb_emp";
	        // executeUpdate 专门执行DML语句（insert、delete、update）,返回影响数据库中记录的条数
	int count = stmt.executeUpdate(sql);
	```

	

5. 处理查询结果集

6. 释放资源（关闭连接）

	```java
	// 从小到大关闭
	stmt.close();
	coon.close();
	```


<!--more-->

### 使用资源绑定器绑定属性配置文件

```java
ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
String user = bundle.getString("username");
String pwd = bundle.getString("password");
String db = bundle.getString("db");
String url = bundle.getString("url");
Class.forName(db);
Connection coon = DriverManager.getConnection(url, user, pwd);
Statement stmt = coon.createStatement();
String sql = "select * from defect_alarm";
ResultSet list =  stmt.executeQuery(sql);
      while (list.next()) {
          String time = list.getString("occurrence_time");
        String alarmName = list.getString("alarm_name");
        System.out.println(time+"-"+alarmName);
    }
stmt.close();
coon.close();
```

### SQL注入

> 例如：用户登录功能

登录的 sql 语句

```sql
select * from t_user where loginName = #{username} and password = #{password}
```

sql 注入： 知道用户名 zs，但不知道密码。输入 用户名: zs

输入密码： zs' or '1' ='1

```sql
select * from t_user where loginName = 'zs' and password = 'zs' or '1' = '1'
```



### 解决 sql 注入

> 只要是用户提供的信息不参与SQL语句的编译过程，问题就解决了。那么就必须使用 java.sql.Preparedstatement

```java
ResourceBundle bundle = ResourceBundle.getBundle("jdbc");
String user = bundle.getString("username");
String pwd = bundle.getString("password");
String db = bundle.getString("db");
String url = bundle.getString("url");
Class.forName(db);
Connection coon = DriverManager.getConnection(url, user, pwd);
// 
String sql = "select * from defect_alarm where username=? and pwd=?";
// 预编译的数据库操作对象
PreparedStatement ps = coon.prepareStatement(sql);
// 传值
ps.setString(1, "admin");
ps.setString(2, "123456");


ResultSet list =  ps.executeQuery();
while (list.next()) {
     ...
}
stmt.close();
coon.close();
```

> Preparedstatement 与 Statement 区别？
>
> Statement  编译一个执行一次，只有在业务要求需要用到SQL注入时才用，例如列表升序降序，需要注入 desc asc 关键字（关键字不能用引号包裹）
>
> ```sql
> // 不行
> String sql = "select ename from emp order by ename=?";
> // 可以
> String sql = "select ename from emp order by ename" + keyword;
> ```
>
> Preparedstatement 编译一次可执行N次，编译阶段进行类型检查，效率高于Statement  

### jdbc 事务

> 多个操作数据库动作，要么全部成功，要么全部失败

```java
Class.forName(db);
Connection coon = DriverManager.getConnection(url, user, pwd);
// 关闭事件自动提交
coon.setAutoCommit(false);
try {
    ...
    // 程序正常，提交事务
	coon.commit();
} catch(Exception e) {
    // 程序出错，事务回滚
    coon.rollback();
}
```

#### 行级锁

> （悲观锁）：当我们查询某些数据时，为了得到真实数据，不允许其他线程对这些数据进项操作，可以加上行级锁
>
> （乐观锁）：支持并发，事务也不需要排队，只不过需要一个版本号。如果在操作过程中有其他线程修改我访问的数据，那么我提交事务时会发现版本号根之前的不一样的，于是我回滚操作

```sql
// 此时这条 id=111 的数据，被锁定
select * from emp where id=111 for update 
```

