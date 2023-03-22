---
title: springboot基础
date: 2023-03-02 16:40:00
tags: springboot基础
categories: java
---

### 配置容器

- xml配置容器

	就是spring 的 xml文件中那样的写法

	```xml
	<bean id = "createStudent" class="com.xxx.Student"></bean>
	```

	

- javaConfig配置容器

	采用java类的方式来配置

	```java
	@Configuration // 表示当前类是作为配置文件使用
	public class SpringConfig {
	    /*
	    	创建方法，方法的返回值是对象。在方法上加上@Bean
	    	方法的返回值对象就注入到容器中。方法名是默认的Bean id
	    */
	    @Bean
	    public Student createStudent() {
	        Student stu = new Student();
	        stu.setName("张三");
	        return stu;
	    }
	    
	}
	```

	把XML文件住的的Bean导入到容器

	```java
	@Configuration
	@ImportResource("classpath:applicationContext.xml")
	public class SpringConfig {
	    
	}
	```

	引入外部属性配置文件

	```java
	@Configuration
	@PropertySource("classpath:comfig.properties")
	public class SpringConfig {
	    
	}
	```

	扫描组件注解

```java
@Configuration
@ComponentScan(basePackage="com.xxx.service")
public class SpringConfig {
    
}
```



### spring boot 

> 它是spring中的一个成员，可以简化Spring MVC的使用，他的核心还是IOC容器。

特点：

1. 内嵌 tomcat，jetty，Undertow
2. 提供起步依赖，简化配置
3. 自动配置spring和第三方库
4. 健康检查，统计，外部化配置
5. 不同生成代码，不用使用xml做配置

<!-- more -->

### 创建springboot项目

- 通过 IDEA 方式

- 浏览器访问 start.springboot.io
- 创建一个普通maven项目然后在**pom**文件中加入parent



### spring注解

@SpringBootApplication

> 此注解由 3 个注解组成
>
> - @SpringBootConfiguration 注解标注类 可作为配置文件使用，注册Bean
> 	- @Configuration  
>
> - @EnableAutoConfiguration 启用自动配置
>
> - @ComponentScan  组件扫描器，从主文件位置扫描
>
> 



### spring boot 的配置文件

> 配置文件名： application
>
> 拓展名：（properties(k:v), yml(k: v)）
>
> 两种格式同时存在系统默认读取 application.properties

application.yml

```yml
server:
  port: 18080
  tomcat:
    uri-encoding:  UTF-8
  dzi:
    install:
      path:  /home/image2dzi/
  enable:
    gpu:  false

spring:
  http:
    encoding:
      force: true
      charset: UTF-8
      enabled: true
  servlet:
    multipart:
      enabled: true
      max-file-size: 10240MB
      max-request-size: 11000MB
      file-size-threshold: 512KB
  application:
    name: labelSystem
  datasource:
      username: root
      password: dbajydw1234
      url: jdbc:mysql://192.168.1.232:3306/labelsystem_test?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
      driver-class-name:  com.mysql.cj.jdbc.Driver
      initial-size:  5
  #devtools:
  #  restart:
  #    enabled: true  #设置开启热部署

mybatis:
  typeAliasesPackage: com.pcl.pojo
  mapperLocations: classpath:mapper/*.xml
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
logging:
  file: labelsystem.log
  path: logs/
minio:
  url: http://192.168.1.245:9000/
  username: minioadmin
  password:  minioadmin
#  存储上传的zip文件以供备份/label-zip/xx.zip
  zipBucketName: label-zip
#  存储解压的图片/label-img/datasetName/xx.jpg
  imgBucketName: label-img
#  存储自动标注后的xml文件/label-xml/datasetName/xx.xml
  xmlBucketName: label-xml
#  存储自动标注后的预览图片base64编码/label-baseCode/datasetName/xx.txt
  baseCodeBucketName: label-basecode

#算法有关参数配置
alogrithom:
  #算法访问链接
  url: http://36.7.72.75:9148/Upload

#直接访问的minio存储位置/home
pictureURL: http://192.168.1.245:18088

# 远程访问服务器配置
sftp:
  #服务器ip
  host: 192.168.1.245
  #ssh端口
  port: 22
  #用户名
  username: zkrd
  #密码
  password: zkrd12#$
  # 上传标注文件服务器路径
  #  uploadAnnotateFile: /home/zkrd/label_system/
  uploadAnnotateFile: F:/sampleManagement/
  #连接池参数
  pool:
    #对象池中管理的最多对象个数。默认值是8
    max-total: 10
    #对象池中最大的空闲对象个数。默认值是8
    max-idle: 10
    #对象池中最小的空闲对象个数。默认值是0
    min-idle: 0
# 分页配置
pagehelper:
  helper-dialect: mysql
  reasonable: true
  support-methods-arguments: true
  params: count=countsql

```

#### 配置文件多环境配置

application-dev.yml	开发环境

application-prod.yml	生产环境

application.properties/application.yml

```yml
# 激活使用按个配置文件
spring.profiles.active=dev
```



#### 配置文件数据映射为Java对象

自定义属性

```yaml
sftp:
  #服务器ip
  host: 192.168.1.245
  #ssh端口
  port: 22
  #用户名
  username: zkrd
  #密码
  password: zkrd12#$
```

Java对象中

```java
@ConfigurationProperties(prefix="sftp")
public class SftpConfig {
    private String host;
    private String port;
    private String username;
    private String password;
}
```



#### spring 中获取容器

```java
ConfigurableApplicationContext app = SpringApplication.run(SpringbootTestApplication.class, args);

app.getBean("");
```

#### CommandLineRunner

> 开发中可能会有这样的情景。需要在容器启动后执行一些内容。比如读取配置文件，数据库连接之类的。SpringBoot 给我们提供了两个接口来帮助我们实现这种需求。这两个接口分别为 **CommandLineRunner** 和 **ApplicationRunner**。他们的执行时机为容器启动完成的时候这两个接口中有一个 run 方法，我们只需要实现这个方法即可。这两个接口的不同之处而在于:ApplicationRunner 中 run 方法的参数为 ApplicationArguments，CommandLineRunner
> 接口中run 方法的参数为 String 数组

```java
@Component
public class Runner implements CommandLineRunner {
    @Override
    public void run(String... args) throws Exception {
        System.out.println("我的 Runner run 方法调用了");
    }
}
```

此组件会在主方法之前调用



### web组件

#### 拦截器

> 能拦截 Control 的接口请求

创建 MyInterceptor

```java

public class MyInterceptor implements HandlerInterceptor {
	// 返回 true 放行，返回false 拦截
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return HandlerInterceptor.super.preHandle(request, response, handler);
    }
}
```

注册拦截器

```java
@Configuration
public class MyAppConfig implements WebMvcConfigurer {
    // 添加拦截器对象，注入到容器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
//        WebMvcConfigurer.super.addInterceptors(registry);
        // 创建过滤器，使用自己的 Filter
        MyInterceptor myInterceptor = new MyInterceptor();
        InterceptorRegistration interceptor = registry.addInterceptor(myInterceptor);
        // 指定拦截的URL地址
        String[] path  = {"/*"};
        // 指定不拦截的地址
        String[] excludePath  = {"/login"};
        interceptor.addPathPatterns()
                .addPathPatterns(path)
                .excludePathPatterns(excludePath);
    }
}

```

#### springboot 中使用 servlet

新建servlet

```java
public class TestService extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
        res.setContentType("text/html;charset=utf-8;");
        PrintWriter writer = res.getWriter();
        writer.println("servlet 请求");
    }
}
```

注册servlet

```java
@Configuration
public class RegistorServlet {
    @Bean
    public ServletRegistrationBean ServletRegistrationBean() {
        // ServletRegistrationBean bean = new ServletRegistrationBean<>(new TestService(), "/servlet");
        ServletRegistrationBean bean = new ServletRegistrationBean());
        bean.setServlet(new TestService());
        bean.setUrlMappings("/servlet1","/servlet2");
        return bean;
    }
}
```



#### 过滤器

创建 MyFilter

```java
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("过滤器启动");
        chain.doFilter(request, response);
    }
}
```

注册 Filter

```java
@Configuration
public class RegisterFilter {
    @Bean
    public FilterRegistrationBean filterRegisterBean() {
        final FilterRegistrationBean bean = new FilterRegistrationBean();
        bean.setFilter(new MyFilter());
        bean.addUrlPatterns("/*");
        return bean;
    }
}

```



添加字符集过滤

```java
@Bean
public FilterRegistrationBean filterEncoding() {

    FilterRegistrationBean bean = new FilterRegistrationBean();
    // 框架内置的过滤器 CharacterEncodingFilter
    CharacterEncodingFilter filter = new CharacterEncodingFilter();
    // 指定编码方式
    filter.setEncoding("utf-8");
    // 让 req,res 对象都使用 utf-8
    filter.setForceEncoding(true);
    bean.setFilter(filter);
    bean.addUrlPatterns("/*");
    return bean;
}
```

启用字符集过滤器

```yaml
servlet:
    encoding:
      enabled: false # springboot 默认启用配置了字符集 IOS-8859-1
```

其实我没可以使用spring boot默认的字符集配置，只需要改变默认字符集

```yaml
  servlet:
    encoding:
      enabled: true # springboot 默认配置了字符集 IOS-8859-1
      charset: utf-8
      force: true
```



### 集成mybatis

依赖：创建项目时选择 springWeb, MybaatisFragment, Mysql-Driver

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

配置数据库

```yaml
spring:
  datasource:
    username: root
    password: 123465
    url: jdbc:mysql://127.0.0.1:3306/mydb01?useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
    driver-class-name: com.mysql.cj.jdbc.Driver
    initial-size: 5
```

创建 model，control，service, mapper



#### @Mapper 注解

> 放在 mapper 接口上。每个mapper 都要加。它会自动生成 实现类

#### @MapperScan

> 配置扫描，可以给指定目录下文件添加 @Mapper注解。通常配置在项目主类上

```java
@MapperScan(basePackages = {"com.xxx.mapper", "com.xxx.dao"})
```



#### 指定 xml 文件所在文件目录位置

```yaml
mybatis:
  # 指定mapper文件位置
  mapper-locations: classpath:mapper/*.xml
```

#### 指定日志输出格式

```yaml

configuration:
  log-impl: org.apache.ibatis.logging.log4j.Log4jImpl
```



### springboot事务管理

> 在入口类使用注解 @EnableTransactionManagement 开启事务支持
>
> 在业务方法中 加入 @transactional 表示事务支持。



### 接口架构风格

> API：可以指访问servlet，controller的url，调用其他程序的函数



RESTful 风格

> Representational State Trasfer 【表现层状态转移】
>
> 它是一种架构风格和设计的理念，不是标准
>
> 优点：更简洁，更又层次
>
> **使用url表示资源，使用http动作操作资源**

相关注解：

@PathVariable: 从url中获取数据

@GetMapper: 支持get请求

@PostMapping：支持post请求方式’

@PutMapper: 支持put请求

@DeleteMapping：支持delete请求

@RestController: 符合注解，是@Controller和@ResponseBody组合

​	表示当前类的所有方法都加入 @ResponseBody

```java
/**
 * @PathVariable 获取url中数据
 	{id} 定义路径变量
 * */
@ResponseBody
@GetMapping("/queryStudent/{id}")
public String queryStudent(@PathVariable(value="id") int id) {

    return "id=" + id;
}
```



### 注解总结

#### Spring + SpringMVC + SpringBoot

**创建对象的:**
@Contro1ler: 放在类的上面，创建控制器对象，注入到容器中

@RestContro11er: 放在类的上面，创建控制器对象，注入到容器中。
作用: 复合注解是

@Contro1ler ，@ResponseBody，使用这个注解类的，里面的控制器方法的返回值都是数据

@Service :放在业务层的实现类上面，创建service对象，注入到容器

@Repository : 放在dao层的实现类上面，创建dao对象，放入到容器。 没有使用这个注解，是因为现在使用MyBatis梅架，dao对象是MyBatis通过代理生成的。 不需要使用

@Component: 所以没有使用。放在类的上面，创建此类的对象，放入到容器中。

**赋值的:**

@Value: 简单类型的赋值， 例如 在属性的上面使用@Value("李四”) private string name@vaTue :还可以使用@Value,获取配置文件者的数据(properties或ym])。@Value("sfserver .port}") private Integer port

@Autowired: 引用类型赋值自动注入的，支持byName，byType.默认是byType 。放在属性的上面，也可以放在构造方法的上面。 推荐是放在构造方法的上面给引用类型赋值，使用byName方式。

@Qualifer: @Qualifer都是Spring框架提供的。

@Resource :来自idk中的定义， javax.annotation。 实现引用类型的自动注入， 支持byName，byType.黑认是byWame，如果byName失败， 再使用byType注入。 在属性上面使用



**其他**

@Configuration :放在类的上面，表示这是个配置类，相当于xml配置文件

@Bean: 放在方法的上面， 把方法的返回值对象，注入到spring容器中。

@ImportResource :加载其他的xm配置文件， 把文件中的对象注入到spring容器中

@PropertySource :读取其他的properties属性配置文件

@ComponentScan :扫描器，指定包名，扫描注解的

@ResponseBody: 放在方法的上面，表示方法的返回值是数据，不是视图

@RequestBody : 把请求体中的数据，读取出来， 转为iava对象使用。

@ControlTerAdvice:控制器增强， 放在类的上面， 表示此类提供了方法，可以对controller增强功能。

@ExceptionHandler : 处理异常的，放在方法的上面



**SpringBoot中使用的注解**

@SpringBootConfiguration: 放在启动类上面， 包含了@SpringBootApplication :@EnabTeAutoConfiguration, @ComponentScan

**MyBatis相关的注解**

@Mapper : 放在类的上面 ， 让MyBatis找到接口，创建他的代理对象(MapperScan :放在主类的上面 ， 指定扫描的包， 把这个包中的所有接口都创建代理对象。 对象注入到容器中

@Param :放在dao接口的方法的形参前面，作为命名参数使用的。

**Dubbo注解**

@Dubboservice: 在提供者端使用的，暴露服务的， 放在接口的实现类上面@DubboReference: 在消费者端使用的， 引用远程服务， 放在属性上面使用。@EnableDubbo : 放在主类上面， 表示当前引用启用Dubbo功能。