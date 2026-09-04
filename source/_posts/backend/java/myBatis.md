---
title: mybatis 基础
date: 2023-02-14 01:20:00
tags: mybatis 基础
categories: java
---



## ORM

> O (Object) jvm中的对象
>
> R (Relational) 关系型数据库
>
> M (Mapping) 映射

![image-20230214134743208](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230214134743208.png)

1. 支持定制化 SQL、存储过程、基本映射以及高级映射
2. 避免了几乎所有的JDBC代码中手动设置参数以及获取结果集
3. 支持XML开发，也支持注解式开发。[为了保证sql语句的灵活，所以mybatis大部分是采用XML方式开发。]
4. 将接口和Java 的 POJOs(Plain rdinary Java Object，简单普通的Java对象)映射成数据库中的记录体积小好学: 
5. 两个jar包，两个XML配置文件。
6. 完全做到sal解耦合
7. 提供了基本映射标签
8. 提供了高级映射标签
9. 提供了XML标签，支持动态SQL的编写

<!-- more -->

### 开发第一个 Mybatis 程序

1. resources目录:
	放在这个目录当中的，一般都是资源文件，配置文件，直接放到resources目录下的资源，等同于放到了类的根路径下。

2. 开发步骤

	- 第一步:打包方式jar

	- 第二步:引入依赖
		- mybatis依赖
		- mysgl驱动依赖

	- 第三步:编写mybatis核心配置文件: mybatis-config.xml大

		> 注意:
		>
		> - 第一: 这个文件名不是必须叫做mybatis-config.xml，可以用其他的名字。只是大家都采用这个名字.
		> - 第二:这个文件存放的位置也不是固定的，可以随意，但一般情况下，会放到类的根路径下。

		

3. 从XML 中构建 SglSessionFactory通过官方的这句话，

	> 你能想到什么呢?
	>
	> ​	第一:在MyBatis中一定是有一个很重要的对象，这个对象是: SqlSessionFactory对象。
	>
	> ​	第二: SqlSessionFactory对象的创建需要XML。XML是什么?
	> 它一定是一个配置文件

	

4. mybatis中有两个主要的配置文件:

	> 其中一个是: **mybatis-config.xml**，这是核心配置文件，主要配置连接数据库的信息等。（一个）
	>
	> 另一个是: **XxxxMapper.xml**，这个文件是专门用来编写SQL语句的配置文件。（每个表一个）
	>
	> 在resources 目录下新建，前者引用后者

### **mybatis-config.xml**

> ```java
> sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"), "指定的环境");
> ```
>
> 事务管理：transactionManager.type = "JDBC" / "MANAGED"
>
> 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb01"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--   resource从类的根路径查找     -->
        <mapper resource="CatMapper.xml"/>
    </mappers>
</configuration>
```

#### 数据源(dataSource)

> 为程序提供 Connection 对象。此规范是jdk规定的（要实现 javax.sql.DataSource）
>
> ​		dataSource.type="POOLED" 实际是在指定数据库的连接池。 此外还有（druid,c3p0,dbcp...）
>
> [官方中文文档](https://mybatis.net.cn/configuration.html#environments)

#### **mybatis 自带的数据源**：

- UNPOOLED：不使用数据库连接池技术，每次请求都是创建新的Connection对象
- POOLED：使用mybatis自己实现的数据库连接池
- JNDI：集成其他第三方数据库连接池

#### JNDI 是一套规范，谁实现了它？

> 大部分web容器都实现了它，如 tomcat, webLogic, Jetty, WebSphare

```xml
<dataSource type="JNDI">
    <property name="initial_context" value=""/>
    <property name="data_source" value=""/>

</dataSource>
```

#### 连接池的配置

- `poolMaximumActiveConnections` – 在任意时间可存在的活动（正在使用）连接数量，默认值：10
- `poolMaximumIdleConnections` – 任意时间可能存在的空闲连接数。
- `poolMaximumCheckoutTime` – 在被强制返回之前，池中连接被检出（checked out）时间，默认值：20000 毫秒（即 20 秒）
- `poolTimeToWait` – 这是一个底层设置，如果获取连接花费了相当长的时间，连接池会打印状态日志并重新尝试获取一个连接（避免在误配置的情况下一直失败且不打印日志），默认值：20000 毫秒（即 20 秒）。
- `poolMaximumLocalBadConnectionTolerance` – 这是一个关于坏连接容忍度的底层设置， 作用于每一个尝试从缓存池获取连接的线程。 如果这个线程获取到的是一个坏的连接，那么这个数据源允许这个线程尝试重新获取一个新的连接，但是这个重新尝试的次数不应该超过 `poolMaximumIdleConnections` 与 `poolMaximumLocalBadConnectionTolerance` 之和。 默认值：3（新增于 3.4.5）
- `poolPingQuery` – 发送到数据库的侦测查询，用来检验连接是否正常工作并准备接受请求。默认是“NO PING QUERY SET”，这会导致多数数据库驱动出错时返回恰当的错误消息。
- `poolPingEnabled` – 是否启用侦测查询。若开启，需要设置 `poolPingQuery` 属性为一个可执行的 SQL 语句（最好是一个速度非常快的 SQL 语句），默认值：false。
- `poolPingConnectionsNotUsedFor` – 配置 poolPingQuery 的频率。可以被设置为和数据库连接超时时间一样，来避免不必要的侦测，默认值：0（即所有连接每一时刻都被侦测 — 当然仅当 poolPingEnabled 为 true 时适用）。

```xml
// 配置最大连接数
<property name="poolMaximumActiveConnections" calue="3"/>
// 每隔两秒打印日志，并尝试获取连接对象
<property name="poolTimeToWait" calue="2000"/>
// 强行让某个连接空闲，超时10s设置为空闲状态
<property name="poolMaximumCheckoutTime" calue="10000"/>
<!--
空闲连接数5, 如果有6个空闲连接，会真正关闭一个空闲连接
-->
<property name="poolMaximumIdleConnections" calue="5"/>
```

#### properties 属性配置

> 

```xml
// 设置
<properties>
	<property name="home" value="D://mybatis/resource/" />
</properties>

// 使用
<mappers>
    <!--   resource从类的根路径查找     -->
    <mapper path="${home}CatMapper.xml"/>
</mappers>

```

使用 properties 文件方式配置

在resource 目录下新建 jdbc.properties

```properties
driver=com.mysql.cj.jdbc.Driver
url=jdbc:mysql://localhost:3306/mydb01
username=root
password=123456
```

映射到 mybatis-config.xml 中 property 中

```xml
<properties resource="jdbc.properties" />
```

使用

```xml
<environment id="development">
<transactionManager type="JDBC"/>
<dataSource type="POOLED">
    <property name="driver" value="${driver}"/>
    <property name="url" value="${url}"/>
    <property name="username" value="${username}"/>
    <property name="password" value="${123456}"/>
</dataSource>
</environment>
```



### **CatMapper.xml**

> namespace 指定命名空间，那么多个mapper 文件出现相同的id也不会冲突了

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.mybatis.example.BlogMapper">

    <insert id="insertCar">
        insert into t_car(id, car_num,brand,guide_price, produce_time, car_type)
        values(null, '1003', '丰田霸道',30.0,'2000-10-11','燃油车')
    </insert>

</mapper>
```



#### 程序 CatMapper.xml 中的 insert 语句

```java
SqlSession  sqlSession = null;
try {
    // 获取 sqlSessionFactoryBuilder
    SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
    // 获取 SqlSessionFactory
    SqlSessionFactory sqlSessionFactory1 = sqlSessionFactoryBuilder.build(Resources.getResourceAsStream("mybatis-config.xml"));
    // 获取 SqlSession 对象
    sqlSession = sqlSessionFactory1.openSession();
    int count = sqlSession.insert("insertCar");
    System.out.println("count = " + count);
    // 手动提交
    sqlSession.commit();
    sqlSession.close();
} catch (Exception e) {
    e.printStackTrace();
    sqlSession.rollback();
}
```



> 细节：
>
> mybatis 中sql语句可以省略 ”；“
>
> Resource.getResourceAsStream() 是从 类路径下查找，大部分resource都是这样的
>
> Resource.getResourceAsStream 底层实际上调用的是：
>
> ```java
> InputStream is = Classloader.getSystemClassLoader().getResourceAsStream("xxx.xml");
> ```
>
> 核心配置文件名字不一定非的是 mybatis-config.xml
>
> ```xml
> // 类路径
> <mapper resource="CatMapper.xml"/>
> // 绝对路径
> <mapper utl="file:///d:/CatMapper.xml"/>
> ```
>
> 



### mybatis 事务管理机制

> 配置：
>
> ```xml
> // mybatis自己采用原生JDBC方式管理事务
> <transactionManager type="JDBC"/>
> // mybatis 不在负责事务，交给其他容器管理。如：spring
> <transactionManager type="MANAGED"/>
> ```

### 封装 SqlSession 对象

```java
package utils;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

public class SqlSessionUtil {
    private static SqlSessionFactory sqlSessionFactory;
    // 防止 new 对象
    private SqlSessionUtil(){}

    // 类加载时，解析mtbatis-config.xml 文件。创建 SqlSessionFactory 对象
    static {
        try {
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis-config.xml"));

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /*
    * 获取会话对象
    * */
    public static SqlSession openSession() {
        return sqlSessionFactory.openSession();
    }
}

```



### Mybatis 日志

> settings 标签要出现在 environments 标签之前，STDOUT_LOGGING 为 mybatis 自己实现的日志格式，其他格式需要引入第三方库 如 SLF4J

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```



#### 集成logback 日志框架

1. pom 中引入依赖

	```xml
	<dependency>
	    <groupId>ch.qos.logback</groupId>
	    <artifactId>logback-classic</artifactId>
	    <version>1.2.11</version>
	    <scope>test</scope>
	</dependency>
	```

2. 配置文件 logback.xml/logback-test.xml

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<configuration debug="false">
	    <!--  控制台输出  -->
	    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
	        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
	        <!-- 格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度
	        %msg: 日志消息，%n 换行符  -->
	            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg %n</pattern>
	        </encoder>
	    </appender>
	
	    <!--  mybatis log configure  -->
	    <logger name="com.apache.ibatis" level="TRACE" />
	    <logger name="java.sql.Statement" level="DEBUG" />
	    <logger name="java.sql.PreparedStatement" level="DEBUG" />
	    <logger name="java.sql.Connection" level="DEBUG" />
	
	    <!--  日志输出级别， TRACE < DEBUG < INFO < WARN < ERROR  -->
	    <root level="DEBUG">
	        <appender-ref ref="STDOUT" />
	        <appender-ref ref="FILE" />
	    </root>
	</configuration>
	```

	



### 使用 mybatis 完成CRUD

> 

1. C：Create 增

	使用 #{} 代价 ？占位符。 执行 sqlSessqion.insert() 时可传入一个Map集合,此时 #{map集合的key}

	```xml
	<insert id="insertCar">
	    insert into t_car(id, car_num,brand,guide_price, produce_time, car_type)
	    values(null, #{}, #{},#{},#{},#{})
	</insert>
	```

	建立 pojo类

	```java
	public class Car {
	    // 建议使用包装类，防止NULL问我呢提
	    private Long id;
	    private String carNum;
	    private String brand;
	    private Double guidePrice;
	    private String produceTime;
	    private String carType;
	
	    public Car() {}
	
	    public Car(Long id, String carNum, String brand, Double guidePrice, String produceTime, String carType) {
	        this.id = id;
	        this.carNum = carNum;
	        this.brand = brand;
	        this.guidePrice = guidePrice;
	        this.produceTime = produceTime;
	        this.carType = carType;
	    }
	
	    @Override
	    public String toString() {
	        return "Car{" +
	                "id=" + id +
	                ", carNum='" + carNum + '\'' +
	                ", brand='" + brand + '\'' +
	                ", guidePrice=" + guidePrice +
	                ", produceTime='" + produceTime + '\'' +
	                ", carType='" + carType + '\'' +
	                '}';
	    }
	
	    public Long getId() {
	        return id;
	    }
	
	    public void setId(Long id) {
	        this.id = id;
	    }
	
	    public String getCarNum() {
	        return carNum;
	    }
	
	    public void setCarNum(String carNum) {
	        this.carNum = carNum;
	    }
	
	    public String getBrand() {
	        return brand;
	    }
	
	    public void setBrand(String brand) {
	        this.brand = brand;
	    }
	
	    public Double getGuidePrice() {
	        return guidePrice;
	    }
	
	    public void setGuidePrice(Double guidePrice) {
	        this.guidePrice = guidePrice;
	    }
	
	    public String getProduceTime() {
	        return produceTime;
	    }
	
	    public void setProduceTime(String produceTime) {
	        this.produceTime = produceTime;
	    }
	
	    public String getCarType() {
	        return carType;
	    }
	
	    public void setCarType(String carType) {
	        this.carType = carType;
	    }
	}
	
	```

	pojo 类实例化

	```java
	Car car = new Car(null, "3333", "保时捷",40.0,"2020-10-12","新能源");
	/*
		执行 sqlSessqion.insert() 时传入 car ,#{}中写pojo类的属性名
	*/
	```

	

2. R:Retrieve 查

	单个查询

	```xml
	<select id="selectCar" resultType="com.pojo.Car">
	    select * from t_car where id=#{id}
	</select>
	
	// sqlSession.selectOne("selectCar", 37)
	<!-- 
		老版本 mybatis 好像会有 字段对应问题 如 pojo 类中的小驼峰命名与数据库的下划线命名会导致查询不到字段的值， 查询时需要用 as 语法赋值别名与 pojo类中的一致
	-->
	```

	列表查询

	```xml
	<select id="selectCarAll" resultType="com.pojo.Car">
	    select * from t_car
	</select>
	
	List<Car> rs = sqlSession.selectList("selectCarAll");
	```

	

3. U:Update 改

	```xml
	<update id="updateCar">
	update t_car set car_num=#{carNum},brand=#{brand},guide_price=#{guidePrice},produce_time=#{produceTime},car_type=#{carType} where id = #{id}
	</update>
	
	// sqlSession.update("updateCar", car)
	```

	

4. D：Delete 删

	```xml
	<delete id="deleteCar">
	    delete from t_car where id = #{id}
	</delete>
	
	// 如果#{} 只有一个，那么中间的属性可以随便写，最好见名知意
	sqlSession.delete("deleteCar", 37)
	```

	



### dom4j 解析xml文件

#### 依赖

```xml
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.4</version>
</dependency>
<!--   dom4j缺少的依赖包     -->
<dependency>
    <groupId>jaxen</groupId>
    <artifactId>jaxen</artifactId>
    <version>2.0.0</version>
</dependency>
```

#### 配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <properties resource="jdbc.properties" />

    <!--    <settings>-->
    <!--        <setting name="logImpl" value="STDOUT_LOGGING"/>-->
    <!--        <setting name="logImpl" value="SLF4J"/>-->
    <!--    </settings>-->
    <environments default="development">

        <environment id="production">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
        <!--    一个环境对应一个 SqlSessionFactory 对象    -->
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${driver}"/>
                <property name="url" value="${url}"/>
                <property name="username" value="${username}"/>
                <property name="password" value="${password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--   resource从类的根路径查找     -->
        <mapper resource="CatMapper.xml"/>
    </mappers>
</configuration>
```

#### 解析xml配置文件

```java
public class ReadXMLTest {
    public static void main(String[] args) throws DocumentException {

    }
    public static Document getDocument(String url) throws DocumentException {
        SAXReader reader = new SAXReader();
        return  reader.read(ClassLoader.getSystemClassLoader().getResourceAsStream(url));
    }
    public static void parseXML() throws DocumentException {
        Document document = getDocument("mybatis-config.xml");
        Element environments = (Element) document.selectSingleNode("/configuration/environments");
        String defaultId = environments.attributeValue("default");
        String xPath = "/configuration/environments/environment[@id='"+defaultId+"']";
        // 获取 environments 中id == environments default 值 environment
        Element environment = (Element) document.selectSingleNode(xPath);
        System.out.println("environment id = " + environment.attributeValue("id"));

        // 获取事务类型
        String transactionType = environment.element("transactionManager").attributeValue("type");
        System.out.println("transactionType = " + transactionType);
        // 获取 dataSource
        Element dataSource = environment.element("dataSource");
        String dataSourceType = dataSource.attributeValue("type");
        System.out.println("dataSourceType = " + dataSourceType);
        // 获取所有 property
        List<Element> properties = dataSource.elements("property");
        properties.forEach(property -> {
            System.out.println(property.attributeValue("name")+"-"+property.attributeValue("value"));
        });
        // 获取所有 mapper
        List<Node> nodes = document.selectNodes("//mapper");
        nodes.forEach(node -> {
            String resource = ((Element) node).attributeValue("resource");
            System.out.println("resource = " + resource);
        });

        // 获取某节点所有元素 某节点元素.elements();
        // 获取元素中的文本  element.getTextTrim();
    }
}

```



### javassist

> 可以在内存中生成类，接口，添加方法。。。
>
> Dao 层的接口实现类 代码非常固定，简单。可以使用 javassist 生成实现类



```java
 // 获取类池，用来生成class
ClassPool pool = ClassPool.getDefault();
// 制造类，传入类名
CtClass ctClass = pool.makeClass("com.javassist.Test.Math");
// 制造方法
String methodCode = "public void insert() {System.out.print(111);}";
CtMethod ctMethod = CtMethod.make(methodCode, ctClass);
// 将方法加入到类
ctClass.addMethod(ctMethod);
// 内存中生产 class, 返回类加载
Class classMath = ctClass.toClass();

// 类加载
// Class classMath = Class.forName("com.javassist.Test.Math");
// 实例化
Object cm = classMath.newInstance();

// 获取方法
Method md = classMath.getDeclaredMethod("insert");
// 调用方法
md.invoke(cm,null);
```

为 接口 Delete 添加 实现类 DeleteImpl

```java
// 获取类池
ClassPool classPool = ClassPool.getDefault();
// 制造类
CtClass ctClass = classPool.makeClass("com.javassist.impl,DeleteImpl");
// 制造接口
CtClass anInterface = classPool.makeInterface("com.javassist.Delete");
// 添加接口到类  DeleteImpl implements Delete
ctClass.addInterface(anInterface);

// 实现方法
// 制造方法
CtMethod method = CtMethod.make("public void delete(){System.out.println(\"delete\");}",ctClass);
// 将方法添加到类
ctClass.addMethod(method);

// 在内存中生成类，加载到JVM
Class classzz = ctClass.toClass();
Delete deleteDao = (Delete)classzz.newInstance();
deleteDao.delete();
```

mybatis 底层使用这个方式帮我们自动生成 DAO 接口的代理类，然后实现这个类的实现类

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--要想使用mapper机制，namespace 必须是对应DAO接口的包名-->
<mapper namespace="com.bank.dao.AccountDao">

    <!--  要想使用mapper 机制， 语句id 必须是DAO接口的方法名  -->
    <update id="updateByActno">
        update t_act set balance=#{balance} where actno = #{actno}
    </update>

    <select id="selectByActno" resultType="com.bank.pojo.Account">
        select * from t_act where actno=#{actno}
    </select>

</mapper>
```





### ThreadLocal

> 每一个线程都有属于自己的 SqlSession ,改造 SqlSessionUtils

```java
public class SqlSessionUtil {
    private static SqlSessionFactory sqlSessionFactory;
    // 全局的，服务器级别的，一个服务器当中定义一个即可
    private static ThreadLocal<SqlSession> local = new ThreadLocal<>();
    // 防止 new 对象
    private SqlSessionUtil(){}

    // 类加载时，解析mtbatis-config.xml 文件。创建 SqlSessionFactory 对象
    static {
        try {
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(Resources.getResourceAsStream("mybatis-config.xml"));

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    /**
    * 获取会话对象, 使用 ThreadLocal   全局唯一
    * */
    public static SqlSession openSession() {
        SqlSession sqlSession = local.get();
        if (sqlSession == null) {
            sqlSession = sqlSessionFactory.openSession();
            // 绑定线程，同一线程拿到对象一样
            local.set(sqlSession);
        }
        return sqlSession;
    }

    /**
     * 关闭SqlSession 对象，从当前线程移出SqlSession 对象
     * */
    public static void close(SqlSession sqlSession) {
        if (sqlSession != null) {
            sqlSession.close();
            // 移出SqlSession 对象和当前线程的绑定， 因为mysql有线程池
            local.remove();
        }
    }
}

```



### mybatis 小技巧



#### #{}，${} 区别

> #{}：底层使用 PreparedStatement。先编译再给SQL语句？传值。避免SQL注入
>
> ${}：底层使用 Statement。先精选SQL语句拼接，然后再对SQL 语句进行白泥一。存在SQL注入风险

查询排序时会用到 ${}

分表查询拼接表名查询，如：t_log20201012，前端传日期，后端拼接

#### 批量删除

1. or

	```sql
	delete from t_car where id=1 or id=2 or id=3;
	```

2. int

	```sql
	delete from t_car where id in (1,2,3);
	
	int deleteBatch(String ids);// 多id,分割
	```



#### 模糊查询

```sql
select * from t_car where brand like '%比亚迪%'

1、
'%#{brand}%'
2、concat函数，mysql 数据库中的函数，专门字符拼接
concat('%',#{brand}, '%')
3、
brand like concat('%',#{brand}, '%')
4、
"%"#{brand}"%"

List<Car> selectByBrandLike(Sreing brand);
```

#### 别名配置

```xml
<!--
    mybatis-config.xml	
	别名不区分大小写， namespace不能使用别名
	type: 指定给哪个类型起别名
 	alias：指定别名，省略这个属性，别名就是类的简明

如：下面这个简明 Car
-->
<typeAliases>
    // package可以将此包下所有类自动起别名，默认都是类简名
    <package name="com.java.bank">
    
	<typeAlias type="com.java.bank.Car" alias="aaa" />
</typeAliases>


<!--
	使用
	CaeMapper.xml
--> 
<select id="selectByCarType" resultType="Aaa">
	...
</select>

```



#### mapper配置

```xml
// 类的根路径下查找资源
<mapper resource="xxx.xml" />
// 绝对路径
<mapper url="file:///xxx.xml" />
// 全限定接口名，必须带有包名。然后从接口名同路径查找,并且和接口名一致 (Transfer.xml)
<mapper class="com.java.bank.Transfer" />

// 将包下所有xml文件自动配置。【xml文件与接口同包下且名字相同】
<package name="com.java.bank.mapper" />
```



#### 配置模板文件

> 去 settings -》 FIle and code Template 新增模板
>
> alt+insert 新建快捷键



#### 使用自动生成的主键

> 自动生成一条自增数据，id自增我们就不知道生成的是多少。如果我们要用到id，难道还要再查一次么？

配置生成主键

```xml
// useGeneratedKeys: 开启使用自动生成主键
// keyProperty：将自增主键赋值给 id 属性
<insert id="insertCarUseGeneratedKeys" useGeneratedKeys="true" keyProperty="id">
	insert into t_car values(null, #{carNum}, #{brand}, #{guidePrice}, #{produceTime}, #{carType})
</insert>
```

获取主键值

```java
SqlSession sqlSession = SqlSessionUtil.openSession();
CarMapper mapper = sqlSession.getMapper(CarMapper.class);
Car car = new Car(null, "9911", "凯美瑞", 30.0, "2020-10-10");
mapper.insertCarUseGenneratedKey(car);

System.out.println(car);// 会输出id xxx

sqlSession.commit();
sqlSession.close();
```



#### mybatis 参数处理

##### 简单类型参数

> 7中基本类型＋包装类+String+两种时间类型
>
> byte,short,int,long,float,double,char
>
> Byte,Short,Integer,Long,Float,Double,Character
>
> String
>
> java,util.Date, java.sql.Date

当接口中方法参数只有一个，并且都是简单类型时。此时mybatis 可以自动推断，不用指定参数类型

```java
public interface Student {
    List<Student> selectById(Long id);
    List<Student> selectByName(String name);
    List<Student> selectByTime(Date time);
    List<Student> selectById(boolean id);
}

SqlSession sqlSession = SqlSessionUtil.openSession();
CarMapper mapper = sqlSession.getMapper(CarMapper.class);
mapper.selectById(1000)
```

```xml
<!--
	parameterType 指定参数类型，可以省略 mybatis可以自动推断。关于类型 mtbatis 内置了很多类型的别名，可以参考中文文档
-->
<select id="selectById" resultType="Student" parameterType="long">
	select * from t_student where id= #{id}
</select>

// 可以这样写，指定参数类型和字段在数据库的类型
id=#{id, javaType=String,jdbcType=VARCHAR}

```



##### 参数是 Map 类型

> mybatis 也能自动类型推断

```java
list<Map> insertStudentByMap(Map map);


Map<Stirng> map = new Map();
map.add("xx");
mapper.insertStudentByMap(map);
```

```xml
<insert id="insertStudentByMap" parameterType="map" >
	insert into t_student(id, name,age,sex,birth,height) values(null,#{},#{},#{},#{},#{})
</insert>
```



##### 参数是实体

> 参数是一个 pojo 实体类,此时mybatis 也可以自动类型推断



#### 参数之多参数

> mybatis 会创建一个数组把多个参数放进去
>
> map.put("arg0", name);
>
> map.put("arg1", sex);
>
> map.put("arg2", xxx);

```java
List<Student> selectByNameAndSex(String name, Chaecter sex);
```

```xml
<select id="selectByNameAndSex" resultTpet="Student">
    // param1,param2也行，低版本可能使用的是 #{0}#{1}
	select * from t_student where name=#{arg0} and sex=#{arg1}
</select>
```

#### 参数之 @params 注解

> 底层实现
>
> map.put("name", "xxx");
>
> map.put("sex", "男")

```java
List<Student> selectByNameAndSex(@Param("name") String name, @Param("sex") Chaecter sex);
```

```xml
<select id="selectByNameAndSex" resultTpet="Student">
     // param1,param2也行
	select * from t_student where name=#{name} and sex=#{sex}
</select>
```



### Mybatis 查询专题

#### 返回Car

> 查询返回pojo实体 Car。 模糊查询返回必须采用List 集合接收，因为可能返回多个结果。 如果单个查询用List 集合接收，这样是可以的

```java
Car selectById(String id);
List<Car>selectAll();
```

```xml
// 数据库的下划线命名与pojo类的小驼峰命名冲突可能返回null 值
<select id="selectById" resultType="car">
	select * from t_car where id=#{id}
</select>

<select id="selectAll" resultType="car">
	select * from t_car
</select>
```



#### 返回Map

> 返回map, key 是表字段名，值是对应属性值

```java
// 返回一个map
Map<String, Object> selectByIdRetMap(Long id);
// 返回多个map
List<Map<String, Object>> selectByIdRetListMap(Long id);
// 返回map套map,大map键是小map的key
Map<Long,Map<String, Object>> selectByIdRetBigMap(Long id);

```

```xml

<select id="selectByIdRetMap" resultType="map">
	select * from t_car where id=#{id}
</select>

// 这个resultType还是map
<select id="selectByIdRetListMap" resultType="map">
	select * from t_car
</select>

<select id="selectByIdRetBigMap" resultType="map">
	select * from t_car
</select>
```



#### 结果映射

> 当数据库的下划线命名与pojo类的小驼峰命名冲突可能返回null 值的情况，
>
> 普通就是在查询是添加别名，当时字段多时比较麻烦
>
> 

```java
List<Car> selectAllByResultMap();
```

```xml
<!--
	专门定义一个结果映射指定数据库中字段名对应java pojo类的属性名对应关系
	type: 用来指定pojo类类名
	id: 指定resultMap的唯一标识。在select标签carResultMap中使用

-->
<resultMap id="carResultMap" type="car" ></resultMap>
	// 如果有主键用此标签配置，可以提高mybatis执行效率
	<id property="id" column="id">
	// property pojo类中字段名
	// column 数据库字段名
	<result property="" column="" />
     // 如果两个属性值相同可以省略
     <result property="carType" column="car_type" javaType="string" jdbcType="VARCHAR" />
<select id="selectAllByResultMap" resultType="map" resultMap="carResultMap">
	select * from t_car
</select>
```

#### **开启驼峰命名自动映射**

> java命名：小驼峰   eg: myMethodPost
>
> mysql命名：将小驼峰改为 下划线：my_method_post

mybatis-config.xml

```xml
<settings>
	<setting name="mapUndersourceToCamelCase" value="true" />
</settings>
```



#### 返回总记录条数

> 查询所有，但是要返回总记录条数

```xml
<select id="selectTotal" resultType="long">
	select count(*) from t_car
</select>
```



### 动态SQL

> 需要拼接sql  如批量删除
>
> ```sql
> // id 数组是动态的
> delete from t_car where id in (1,2,3...)
> ```
>
> 

#### if标签

```java
List<Car> selectByMultiCondition (
    @Param("brand") String brand, 
    @Param("guidePrice") Double guidePrice, 
    @Param("carType") String carType
);
```

```xml
<select id="selectByMultiCondition" resultType="Car">
    <!-- test 属性是必须的， true拼接，false不拼接
		不能使用 && 使用 and 
		加上 1=1 为了防止where 后边没有东西sql报错
-->
    select * from t_car where 1=1
	<if test="brand != null and brand !=' '">
        and brand like "%"#{brand}"%"
    </if>
    <if test="guidePrice != null and guidePrice != ' '">
    	and guide_price like "%"#{guidePrice}"%"
    </if>
     <if test="carType != null and carType != ' '">
        and car_type like "%"#{carType}"%"
    </if>
</select>
```



#### where 标签

> where标签的作用: 让where子句更加动态智能所有条件都为空时，where标签保证不会生成where子句.**自动去除某些条件前面多余的and或or**。

```xml
<select id="selectByMultiCondition" resultType="Car">
    
    select * from t_car
    <!-- 专门负责where子语句动态生成-->
    <where>
    	<if test="brand != null and brand !=' '">
            brand like "%"#{brand}"%"
        </if>
        <if test="guidePrice != null and guidePrice != ' '">
            and guide_price like "%"#{guidePrice}"%"
        </if>
         <if test="carType != null and carType != ' '">
            and car_type like "%"#{carType}"%"
        </if>
    </where>
	
</select>
```



#### trim标签

> trim标签的属性
>
> - prefix:在trim标签中的语句前添加前缀
> - suffix: 在trim标签中的语句后添加后缀
> - prefixOverrides: 去掉前缀
> - suffixOverrides: 去掉后缀

```xml
<select id="selectByMultiCondition" resultType="Car">
    
    select * from t_car
    <!-- 
	prefix="where"：所有子语句加前缀 where
    suffixOverrides="and|or"：所有子语句去掉后缀 and / or
 -->
    <trim prefix="where" suffixOverrides="and|or">
    	<if test="brand != null and brand !=' '">
            brand like "%"#{brand}"%" and
        </if>
        <if test="guidePrice != null and guidePrice != ' '">
            guide_price = #{guidePrice} and
        </if>
         <if test="carType != null and carType != ' '">
            car_type = #{carType}
        </if>
    </trim>
	
</select>
```



#### set 标签

> **主要使用在update语句当中**，用来生成set关键字，同时去掉最后多余的 "," 比如我们只更新提交的不为空的字段，如果提交的数据是空或者 null，那么这个字段我们将不更新。

```xml
<updateid="updateWithSet"
    update t car
    <set>
        <if test="carlum != null and carNum != ' '">
            car num = #{carlum},
        </if>
        <if test="brand != null and brand != ' '">
            brand = #{fbrand},
        </if>
        <if test="guidePrice != null and guidePrice != ' '">
            guide_price = #{guidePrice},
        </if>
        <if test="produceTime != null and produceTime != ' '">
            produce_time = #{produceTimel},
        </if>
        <if test="carType != null and carType != ' '">
            car_type = #{fcarTypel},
        </if>
    </set>
    where id = #{id}
</update>
```

#### choose when otherwise

> 三个标签通常一起使用，相当于if else, 只要有一个条件成立，就结束

```xml
<select id="" >
	select * from t_car
    <where>
        // 只会选择一个分支
    	<choose>
        	<when test="brand !=null and brand!=' '">
            	brand like "%"#{brand}"%"
            </when>
            <when test="carType !=null and carType!=' '">
            	car_type like "%"#{carType}"%"
            </when>
            <otherwise>
            	produce_time = #{produceTimel},
            </otherwise>
        </choose>
    </where>
</select>
```



#### 批量删除

> 使用 foreach 标签 

```java
int deleteByIds(@Param("ids") String[] ids);
```

```xml
<delete id="deleteByIds" >
    <!--1
		collection: 指定数组/集合
		item: 数组活集合中的元素
		separator: 循环之间的分隔符
		直接使用 ids 会报错，提示可以使用 array/arg, 配置@Params可以使用别名
	-->
	delete from t_car where id in(
    	<foreach collection="ids" item="id" separator="," >
    		#{id}
    	</foreach>
    )
    
    <!--2-->
    delete from t_car where id in
    <foreach collection="array" item="id" separator="," open="(" close=")"
        #{id}
    </foreach>
    
    <!--3-->
    delete from t_car where id
    <foreach collection="array" item="id" separator="or"
        id=#{id}
    </foreach>
    
</delete>
```



#### 批量插入

```java
int insertBatch(@Param("cars") List<Car> cars);
```

```xml
<insert id="insertBatch">
	insert into t_car("id", "brand", "product_price") values
    <foreach collection="cars" item="car" separator="," >
        (null, #{car.brand}, #{car.productPrice})
    </foreach>
    
</insert>
```



#### sql标签，include标签

> sql标签用来声明sql片段，include标签用来将声明的sql片段包含到某个sql语句中
>
> 提高代码复用性

```xml
<sql id="yy" >
	brand,productPrice
</sql>
<select id="xx">
	select <include refid="yy"> from t_car where id=#{id}
</select>
```



### 数据库高级映射

#### 多对一映射

t_stu

| sid  | sname | cid  |
| ---- | ----- | ---- |
| 1    | 张三  | 1000 |
| 2    | 李四  | 1000 |
| 3    | 王五  | 1001 |

```java
public class Student {
    private Integer sid;
    private String sname;
    private Clazz clazz
}
```

t_class

| cid  | cname    |
| ---- | -------- |
| 1000 | 高三一班 |
| 1001 | 高三二班 |

```java
public class Clazz {
    private Integer cid;
    private String cname;
}
```



```xml
<!--
	查询学生带出对应的班级信息
	1、一条SQL，级联属性映射
-->
<resultMap id="stuRsMap" type="Student" >
	<id property="sid" column="sid" />
    <result property="clazz.cid" column="cid" />
    <result property="clazz.cname" column="cname" />
</resultMap>
<select id="selectById" resultMap="stuRsMap">
	select s.sid, s.sname, c.cid, c.cname
    from t_stu
    left join t_clazz c
    on s.cid = c.cid
    where s.sid = #{sid}
</select>
<!--
	查询学生带出对应的班级信息
	2、一条SQL，association
-->
<resultMap id="stuAssociationRsMap" type="Student" >
    // sname同名省略
	<id property="sid" column="sid" />
    <!--
		association:关联，一个student对象关联一个Clazz最小
		property： 提供要映射的POJO类
		javaType: 用来指定要映射的java类型
	-->
    <association property="claszz" javaType="Clazz">
    	<result property="cid" column="cid" />
    	<result property="cname" column="cname" />
    </association>
    
</resultMap>
<select id="selectById" resultMap="stuAssociationRsMap">
	select s.sid, s.sname, c.cid, c.cname
    from t_stu
    left join t_clazz c
    on s.cid = c.cid
    where s.sid = #{sid}
</select>

<!--
	查询学生带出对应的班级信息
	3、两条SQL，多对一分布查询（可复用，支持懒加载）
	开启懒加载：fetchType="lazy"
-->
<resultMap id="stuStepRsMap" type="Student" >
	<id property="sid" column="sid" />
    <association property="claszz" javaType="Clazz" select="xxx.selectStep2" clumn="id" fetchType="lazy">
    	<result property="cid" column="cid" />
    	<result property="cname" column="cname" />
    </association>
    
</resultMap>
<select id="selectStepById" resultMap="stuStepRsMap">
	select sid,sname,cid from t_stu where sid=#{sid}
</select>


// 另一个xml文件中的 查询方法
<select id="selectStep2" resultMap="">
</select>
```

#### 启用全局懒加载

> 信息

```xml
<settings>
	<setting name="lazyLoadingEnabled" value="true" />
</settings>
```



#### 一对多

> 一对多，一在前，一是主表，多是副表

```java
public class Clazz {
    private Integer cid;
    private String cname;
    private List<Student> stus;
}
```

```xml
<!--
	查询班级带出对应的学生信息
	1、
	collection 
-->
<resultMap id="clazzRsMap" type="Clazz">
	<id property="cid" column="cid" />
    <result property="cname" column="cname" />
    <!--
		ofType: 指定集合当中的元素类型
	-->
    <collection property="stus" ofType="Student" column="cid">
    	<id property="sid" column="sid" />
        <result property="sname" column="sname" />
    </collection>
</resultMap>
<select id="selectByCollection" resultMap="">
	select c.id,c.name,s.sid,s.sname 
    from t_clazz
    left join t_stu s
    on c.cid = s.cid
    where c.cid = #{cid}
</select>

<!--
	查询班级带出对应的学生信息
	2、
	分布查询
-->
<resultMap id="stuStepRsMap" type="Student" >
	<id property="sid" column="sid" />
    <collection property="stus" ofType="Student" select="selectStep2">
    	<id property="sid" column="sid" />
        <result property="sname" column="sname" />
    </collection>
    
</resultMap>
<select id="selectStepById" resultMap="stuStepRsMap">
	select cname,cid from t_clazz where cid=#{cid}
</select>


// 另一个xml文件中的 查询方法
<select id="selectStep2" resultType="Student">
    select * from t_stu where cid=#{cid}
</select>
```



### mybatis 缓存

> 执行SQL 查询的时候，将查询结果放到缓存中。如果下次还是执行完全相同的sql语句。直接从缓存拿，不从硬盘找数据

mybatis 缓存包括

1. 一级缓存：将查询数据存到SqlSession 对象 , 针对当前会话，默认开启

	只要两次调用相同sql查询。

	**啥时候以及缓存失败？**

	第一次DQL和第二次DQL之间做了下面任意一件事，都会清空一级缓存

	- 执行　sqlSession,cleanChache()
	- 执行 insert,delete,update 任意一个语句

2. 二级缓存: 将查询数据存到SqlSessionFactory  针对所有会话

	```xml
	//1、 默认就是 true 无需全局配置
	<setting name="chachceEnable" value="true">
	//2、 使用二级缓存的mapper.xml文件需局部配置 <cache />
	//3、 使用二级缓存的实体类必须是可序列化，既必须实现 java.io.Serializable
	//4、 Sqlsession 对象关闭或提交后，一级缓存数据在会被写到二级缓存
	```

	

3. 第三方缓存，三级缓存：三级缓存是用来代替mybatis 自带的二级缓存

	常见：EhCache,Memcache



### myBatis 逆向工程

> 所谓的逆向工程是: **根据数据库表**逆向生成**Java的pojo类**，**SalMapperxml文件**，以及**Mapper接口类**等.
>
> 要完成这个工作，需要借助别人写好的逆向工程插件。
>
> 思考:使用这个插件的话，需要给这个插件配置哪些信息?
>
> - ·pojo类名、包名以及生成位置.
> - SglMapper.xml文件名以及生成位置
> 	·
> - Mapper接口名以及生成位置。
> - 连接数据库的信息。
> - 指定哪些表参与逆向工程

新建一个maven项目

修改 pom.xml

```xml
<packaging>jar</packaging>

<!--定制构建过程-->
<build>
    <!--可配置多个插件-->
    <plugins>
        <!--其中的一个插件: mybatis逆向工程插件-->
        <plugin>
            <!--插件的GAV坐标-->
            <groupId>org.mybatis.generator</groupId>
             <artifactId>mybatis-generator-maven-plugin</artifactId>
             <version>1.4.1</version>
            <!--允许覆盖-->
            <configuration>
            	<overwrite>true</overwrite>
        	</configuration>
        	<!--插件的依赖-->
            <dependencies>
                <!--mysgl驱动依赖-->
                <dependency>
                    <groupId>mysgl</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.30</version>
                 </dependency>
            </dependencies>
        </plugin>
    </plugins>
</build>
```

新增 generatorConfig.xml，放在类的根路径

```xml
<?xml version="1.0” encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
	PUBLIC"-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
	"http://mybatis.org/dtd/mybatis-generator-config 1 0.dtd">
<generatorConfiguration>
<!--
    targetRuntime有两个值 :
    MyBatis3simple:生成的是基础版，只有基本的增删改查。
    MyBatis3: 生成的是增强版，除了基本的增删改查之外还有复杂的增删改查。
-->
    <context id="DB2Tables" targetRuntime="MyBatis3">
        <!--防止生成重复代码-->
        <plugin type="org.mybatis.generator.plugins.UnmergeableXmlMappersPlugin"/>

        <commentGenerator>

            <!--是否去掉生成日期-->
            <property name="suppressDate" value="true"/>
            <!--是否去除注释-->
            <property name="suppressAl1Comments" value="true"/>

        </commentGenerator>
        <!--连接数据库信息-->
        <jdbcConnection 
            driverclass="com.mysql.cj.jdbc.Driver"
            connectionURL="jdbc:mysql://Tocathost:3306/powernode"
             userId="root"
             password="root">
		</jdbcConnection>
    	<!-- 生成pojo包名和位置 -->
         <javaModelGenerator 
              targetPackage="com.powernode.mybatis.pojo"
              targetProject="src/main/java"
            >
            <!--是否开启子包-->
            <property name="enableSubPackages" value="true"/>
            <!--是否去除字段名的前后空白-->
            <property name="trimStrings" value="true"/>
		</javaModelGenerator>
          <!-- 生成SQL映射文件的包名和位置 -->
          <sqlMapGenerator 
              targetPackage="com.powernode.mybatis.mappen" 
              targetProject="src/main/resources"
           >
               <!--是否开启子包-->
               <property name="enableSubPackages" value="true"/>
           </sqlMapGenerator>
        <!-- 生成Mapper接口的包名和位置-->
        <javaClientGenerator
            type="xmIMapper"
            targetPackage="com.powernode.mybatis .mapper"
            targetProject="src/main/java"
        >
        <property name="enableSubPackages" value="true"/>
        </javaClientGenerator>
        <!-- 表名和对应的实体类名-->
         <table tableName="t_car" domainObjectName="Car"/>
    </context>

</generatorConfiguration>
```

从maven找到插件 mybatis-generator,展开。 双击mybatis-generator:generate

> MyBatis逆向工程dao接口只生成两个方法，原因：
> 1.是你的mysql版本太高了，换成6一下的版本即可
> 2.看看你的表有没有设置主键，如果没有设置一下即可 
> 3.如果想使用高版本的mysql在url后面加nullCatalogMeansCurrent=true

### PAgeHelper 分页插件

#### limit 分页

```sql
/*
	起始位置(从0开始),每页条数
	1页				n 页
	startIndex 0	(n-1)*page
	page 10			page
*/
SELECT * FROM defect_alarm LIMIT 0,3

	
```

#### 使用 pageHelper

> ```xml
> <dependency>
>   <groupId>com.github.pagehelper</groupId>
>   <artifactId>pagehelper</artifactId>
>   <version>5.1.10</version>
> </dependency>
> ```

mybatis-conf.xml

```xml
<plugins>
	<plugin intercepetor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```



### 注解是开发

> 可以不用写 mapper.xml 文件了。但只是针对简单的语句

```java
@Insert("insert into t_car values(null, #{carNum},#{brand})")
int insertCar(Car car);

@Delete("delete from t_car where id=#{id}")
int deleteById(Long id);

@Update("update t_car set car_num=#{carNum},brand=#{brand} where id = #{id}")
int update (Car car);

// 没有配置小驼峰匹配时，可以这样加映射
@Select("select * from t_car where id = #{id}")
@results({
    @Result(property="", column=""),
})
Car selectById(Long id);
```

