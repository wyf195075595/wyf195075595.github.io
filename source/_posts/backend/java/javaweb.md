---
title: jdbcweb基础
date: 2023-02-10 13:00:00
tags: jdbcweb基础
categories: java
---



> servlet 是JavaEE 规范之一。
>
> javaEE 目前最高版本时javaEE8,它被Oracle捐献给Apache。之后被改名为 Jakarta EE。从此没有javaEE 了
>
> javaEE8中的Servlet类名：javax.servlet.Servlet
>
> javaEE9中的Servlet类名：jakarta.servlet.Servlet

### 关于系统架构

1. 系统架构包括

	- C/S 架构

		就是需要安装的软件

	- B/S 架构

		就是运行在浏览器的页面



### Tomcat

> 开源免费的轻量级ＷＥＢ服务器

#### tomcat 目录

- bin：存放命令文件
	- .bat 文件，批量执行windows DOS命令
	- .sh 文件，批量执行 Linux 命令
	- 
- conf：配置文件
- lib：tomcat核心程序目录
- logs：日志目录
- temp：临时目录
- webapps：存放web应用
- work：存放jsp文件翻译之后的Java文件以及编译之后的class文件

配置tomcat环境

1. CATALINA_HOME=D:\tomcat\apache-tomcat-10.0.27
2. JAVA＿HOME=C:\Program Files\Java\jdk1.8.0_321
3. JRE_HOME=%JAVA＿HOME%\jre

#### 运行

```
startup.bat
```

**出现中文乱码**

> 修改conf/logging.properties
>
> java.util.logging.ConsoleHandler.encoding = GBK

<!--more-->

### 开发一个带有servlet的webapp

1. 在webapps 目录下，创建一个目录project，这就是web根目录

2. 根目录下新建 WEB-INF 文件夹

3. WEB-INF目录下传创建 classes 目录：放置字节码文件

4. WEB-INF下新建 lib文件夹：webapp需要第三方jar包时，就放置到此处

5. WEB-INF新建 web.xml 文件：配置文件，描述了路径与类的对应关系

6. 编写Java程序，程序必须实现servlet接口。接口规范不在javaSE，javaEE文档可查看，在tomcat包中  /lib/servlet-api.jar. 解压后 /jakarta/servlet/Servlet.class 就是我们要实现的接口

7. 编写 servlet 编译问题：

	> jakarta包不存在，需要配置环境变量
	>
	> classpath=.;.../tomcat/lib\servlet.jar

8. 将编译后的Java文件放到 classes 下

9. 配置webxml 文件，让路径与类名关联（注册servlet类）



### IDE中开发一个servlet

> **新建 maven webapp 项目不生成 sec目录？**
>
> 找到Build, Execution, Deployment，选中Build Tools–>Maven–>Runner，在右侧 VM Options框中写上 -Darchetype=Internal 即可。
>
> 建议使用 tomcat 9，10 可能出现问题

1. 创建空项目 project

2. 添加模块 servlet01

3. 为模块添加框架支持

4. 编写 servlet （studentServlet），发现Servlet 类不存在

	将tomcat lib 文件夹下的 servlet-api.jar 和 jsp-api.jar 加入。在 service方法中连接数据库

5. 在WEB-INF 下新建 lib,并把 jdbc驱动架包放入

6. 在web.xml 文件完成 servlet子类的注册

	```xml
	<servlet>
	    <servlet-name>studentServlet</servlet-name>
	    <servlet-class>javaweb.StudentServlet</servlet-class>
	</servlet>
	<servlet-mapping>
	    <servlet-name>studentServlet</servlet-name>
	    <url-pattern>/servlet/student</url-pattern>
	</servlet-mapping>
	
	```

7. 给一个HTML页面，在HTML页面中编写超链接，点击执行后台的servlet程序。文件放在WEB-INF 上一级目录

	```html
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <title>Title</title>
	</head>
	<body>
	   // 项目名无法动态获取 /project
	  <a href="/project/servlet/student"> student list</a>
	</body>
	</html>
	```

8. 让IDE工具去关联Tomcat服务器，关联的过程中将webapp部署到Tomcat服务器当中

	- IDEA工具右上角，绿色小锤子右边有一个: Add Configuration
	- 左上角加号，点击Tomcat Server --> local
		在弹出的界面中设置服务器Server的参数 (基本上不用动)
	- 在当前窗口中有一个Deployment (点击这个用来部署webapp)，:继续点击加号，部署即可
		修改 Application context为: /project

9. 访问：http://localhost:8080/project/student.html



### servlet 对象生命周期

> 从创建到销毁的过程

在Servlet中提供一个无参的构造函数，启动服务器的时候看构造函数是否执行。经过测试得出结论：**默认情况下，服务器在启动Servlet对象并不会实例化**

如果我们在配置文件给 servlet 增加

```xml
<servlet>
	<load-on-startup>0</load-on-startup>
</servlet>

```

此时，这个我们自己编写的servlet会在启动服务时实例化



**用户第一次请求**

> 此时路径对应的servlet对象会在被实例化，此时tomcat 调用 servlet 对象的 init 方法。servlet 构造函数会执行

**用户第二次及n次请求**

> 此时路径对应的servlet对象init方法没有调用，构造函数也没有调用

用户发送一次请求，service方法就会被调用一次。

关闭服务器的时候，回调 distory 方法



#### init

> 这个初始化操作指挥执行一次，这里我们看可以做：初始化数据库连接池，初始化线程池

#### service

> 用户每次请求都会调用一次

#### destory

> 进行资源关闭，马上要销毁对象时调用

### 适配器模式

> 由于Servlet 接口我们通常只要service 方法，其他方法用不到。但是又必须的重写。如果我们想省略那些用不着的代码，该怎么办？

ServiceAdapter

这个适配器实现Servlet接口，并把service作为抽象方法.

但实际上这个适配器方法已经有现成的了：GenericServlet

```java
package com.javaweb.servlet.adapter;

import jakarta.servlet.*;

import java.io.IOException;

public abstract class ServiceAdapter implements Servlet {
    // 配置项
    private ServletConfig config;
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init execute");
        this.config = servletConfig;
    }

    @Override
    public ServletConfig getServletConfig() {
        return config;
    }

    @Override
    public abstract void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException;

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {
        System.out.println("destroy execute");
    }
}

```

StudentServlet

```java
public class StudentServlet extends ServiceAdapter {

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("servlet service start!");
        servletResponse.setContentType("text/html");
        PrintWriter out  = servletResponse.getWriter();

        out.println("欢迎访问本页面");
    }
}

```

### servletConfig

> ServletConfig 对象中封装了servlet 标签中的配置信息（web.xml 下的servlet配置）
>
> 



### servletContext

> servletContext 接口时 Servlet 规范中的一员，对应其实就是整个Web.xml 文件。
>
> servletContext 比作教室，servlet 就是学生

```java
// 获取上下文对象的方式
// 1
ServletConfig s = this.getServletConfig();
servletContext application = config.getServletContext();

// 2、
this.getServletContext()
```

#### 配置共有的参数

> 在 web.xml 文件中，与servlet 平级,一般是公共的数据，数据量小且几乎不会再修改（应用域）

```xml
<context-param>
	<param-name>pageSize</param-name>
    <param-value>10</param-value>
</context-param>
```

```java
// 存 map.put(k, value)
public void setAttribute(String name, Object value);
// 取 map.get(k)
public void getAttribute(String name);
// 删 map.remove(k)
public void deleteAttribute(String name);

// 调用
application.setAttribute("key", "name");
```



#### 常用方法



1. public String getInitParameter(String name)  通过初始化参数的name获取value
2. public Enumeration\<String> getInitparameterNames()  通过初始化参数的name
3. public ServletContext getServletContext()
4. public String getServletName()
5. getContextPath()  获取上下文的根
6. getRealPath()  获取文件的绝对路径
7. public void log(String message)
8. public void log(String message, Throawble t);

```java

// 获取上下文初始化参数
Enuumeration<Stirng> initParameterNames = application.getInitParameterNames();

while(initParameterNames.hasMoreElements()) {
    String name = initParameterNames.getInitParameter(name);
    String value = application.getInitparmeter(name);
    
}

// 获取文件路径
String realPath = application.getRealPath("/index.html");

// 记录日志, 存储在 Tamcat 根目录下的 logs 文件夹下.如果用的时IDE，则实在IDE中的小猫咪文件夹下（IDE中可以有多个小猫咪服务）
application.log("日志");
application.log("日志",  Throawble t);
```



> 以后取编写 Servlet 类的时候，实际上不会直接继承 GenericServlet 类的，因为我们是B/S 架构。这种系统基于Http 超文本传输协议的，在Servlet规范中提供了一个类叫做 HttpServlet ，专门为Http协议准备的一个Servlet类，所以我们编写时要继承HttpServlet
>
> ```java
> jakarta.servlet.Servlet(接口) 【爷爷】
> jakarte.servlet.GenaricServlet imlements  Servlet(抽象类)【儿子】
> jakarta.servlet.HttpServelt extends GenaricServlet(抽象类)【孙子】
>     
> 我们以后编写 Servlet 需要继承 HttpServlet
> ```
>
> 

**接触到的缓存机制**

1. 常量池

	”abc“现在字符串常量池找，有，直接用，没有新建放入常量池

2. 整数型常量池

	[-127, 127] 共256个Integer类型引用可以直接使用

3. 连接池

	连接数据库的连接对象，新建n个连接对象。每次用户连接时省去新建环节

4. 线程池

5. redis

6. SerevletContext 应用域中存储数据



### http协议

#### URL与URI

> URL：统一资源定位符
>
> ​	https://rank.kkj.cn/Phone74.shtml
>
> URI：统一资源标识符
>
> ​	/Phone74.shtml

#### get与post请求

- get 请求是绝对安全的，应为get是从服务器获取资源。

	- get请求支持缓存

	- 如果希望get请求有缓存怎么办？

		get请求路径不同即可，如路径添加系统毫秒数

- post 请求是危险的，因为post是向服务器发送数据
	
	- post 响应结果不会被缓存



### HttpServlet 源码分析



#### http 包下都有哪些类的接口

- jakarta.servlet.http.HttpServlet (Http协议专用的请求对象)
- jakarta.servlet.http.HttpServletRequest (Http协议专用请求对象)
- jakarta.servlet.http.HttpServletResPonse (Http协议专用响应对象)



### 常用方法

- HttpServletRequest 

	- getMethod() 获取请求方式

	- getRemoteAddr() 获取用户IP

	- setCharacterEncoding(String env)  设置请求体字符集，获取用户中文输入可能出现乱码，此时需要这个方法设置 "UTF-8"

		```java
		res.setCharacterEncoding("UTF-8");
		
		res.setContentType("text/html;charset=UTF-8");
		
		// get 方法请求乱码问题
		tomcat 8开始默认字符集是UTF-8
		 修改 tomcathome/ conf/server.xml
		    Connector 增加 属性 URIEncoding="UTF-8"
		```

	- getContextPath  获取应用根路径

	- getRequestURL  获取请求方式

	- getServletPath  获取 servlet path

	- 获取浏览器用户提交信息

		- String getParameter(String name)【几乎不用】
		- Map<String,string[]>getParameterMap() 【几乎不用】
		- Enumeration\<String>  getParameterNames() 【用的少】
		- String[] getParameterValues(String name) 【常用】

	- 如何存储用户提交的数据

		```java
		// 为避免键值重复
		Map<String, String[]>
		
		key		value
		-----------------
		username  {"abc"}
		userpwd    {"111"}
		aihao	  {"s","d","w"}
		    
		```

		



### 设置 javaweb 项目欢迎页面

> 就是相当于设置默认的主页面,路径不需要以/开始。如果不设置，默认找index.html

```xml
<welcome-file-list>
    <welcome-file>login.html</welcome-file>
</welcome-file-list>
```



### WEB-INF 目录

> 此目录是受保护的，浏览器通过地址不能访问



### 请求域 对象

> 根应用域一样，也有这三个方法

```java
// 存 map.put(k, value)
public void setAttribute(String name, Object value);
// 取 map.get(k)
public void getAttribute(String name);
// 删 map.remove(k)
public void deleteAttribute(String name);

// 调用
application.setAttribute("key", "name");
```



### 转发机制

> 执行了 A servlet 后跳转到 B servlet

1. 获取请求转发器对象

	```java
	// 参数是 web.xml 配置的路径
	RequestDispatcher dispatcher = request.getRequestDispatcher('/b');
	```

2. 调用请求转发器的 forword 方法

	```java
	dispatcher.forword(request, response);
	```

### 重定向

> 重定向请求两次，转发请求一次

```java
response.sendRedirect("/project/dept/list")
```



### 两个Servlet 数据怎么共享？

1. 将数据放到ServletContext 应用域中
2. 将数据放到 request域中，然后转发到另一个servlet



```sql
drop table if exists dept;
create table dept(
deptno int PRIMARY KEY,
dname varchar(255),
loc varchar(255)
);
insert into dept(deptno, dname, loc) values(10,'销售部','北京'); 

insert into dept(deptno, dname, loc) values(20,'研发部','上海'); 
    
insert into dept(deptno, dname, loc) values(30,'技术部','广州'); 
  
insert into dept(deptno, dname, loc) values(40,'媒体部','深圳'); 
    
commit;
select * from dept;
```



### 使用注解方式注册 servlet 

> 每次配置 web.xml 文件，过于繁琐。可以使用 @WebServlet 注解。
>
> 如果参数不需要变化的建议使用注解，如果参数需要频繁变更建议写在配置文件

```java
@WebServlet(name = "studentServlet", urlPatterns = "/servlet/student",
initParams = { @WebInitParam(name = "username", value="root"), @WebInitParam(name = "password", value="123456")} ,loadOnStartup = 1)
public class StudentServlet extends ServiceAdapter {
    ...
}
```

#### 获取注解上的参数

```java

protected void doGet(HttpServletRequest request, HttpServletResponse response) {
    // 获取Servlet Name
    String servletName = getServletName();
    // 获取Servlet path
    String servletPath = resuest.getServletPath();
    
    // 获取初始化参数 initParams
    Enumeration<String> names = getInitParameterNames();
    while(names.hasMoreElements()) {
        String name = names.nextElement();
        String value = getInitParameter(name);
        System.out.println(name + value);
    }
    
}
```

### 使用反射获取类上的注解

```java
Class<?> welcomeServletClass = Class.forName("com.xxxxx.servlet.WelcomeServlet");
// 判断类是否有注解对象，有就获取
boolean annotationPresent = welcomeServletClass.isAnnotationPresent(WebServlet.class);
if(annotationPresent) {
    WebServlet webServletAnnotation = welcomeServletClass.getAnnitation(WebServlet.class);
    Stirng[] value = webServletAnnotation.value();
    for (int i =0; i< value.length; i++) {
        System.out.println(value[i]);
    }
}
```



### 模板方法设计模式

> 某个问题的固定解决方案（可以被重复使用）

**什么是模板方法设计模式?**

> 在模板类的模板方法当中定义核心算法骨架，具体的实现步骤可以延迟到子类当中完成。模板类通常是一个抽象类，模板类当中的模板方法定义核心算法，这个方法通常是final的(但也可以不是final的)
>
> 模板类当中的抽象方法就是不确定实现的方法，这个不确定怎么实现的事儿交给子类去做。

**之前我们是一个请求一个Servlet ，这样会导致我们创建多个Servlet。我们想把多个Servlet 合并成一个Servlet，怎么做？**

```java
@WebServlet({"/list", "/add", "/save", "/delete", "/update"})
public class DepServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {
//        super.service(req, resp);
        // 获取请求路径
        String servletPath = req.getServletPath();

        if("/list".equals(servletPath)) {
            doList(req, res);
        } else if("/add".equals(servletPath)) {
            doAdd(req, res);
        }
    }

    private void doAdd(HttpServletRequest req, HttpServletResponse res)  throws ServletException, IOException {
        PrintWriter out = this.getWriter(res);
        out.println("doAdd");
    }

    private void doList(HttpServletRequest req, HttpServletResponse res)  throws ServletException, IOException {
        PrintWriter out = this.getWriter(res);
        out.println("doList");
    }
    public PrintWriter getWriter(ServletResponse res) {
        res.setContentType("text/html");
        PrintWriter out  = null;
        try {
            out = res.getWriter();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return out;
    }
}

```





### jsp

> 访问 index.jsp 文件实际上是访问 tomcat 翻译生成的 index_jsp.class

> **]SP实际上就是一个Servlet。**
> **i**ndex.sp访问的时候，会自动翻译生成index isp.java，会自动编译生成index isp.cass，那么index isp 这就是一个类。
>
> indexjsp类继承HttplspBase，而HttplspBase类继承的是HttpServlet。所以index jsp类就是一个Servlet类。isp的生命周期和Servlet的生命周期完全相同。完全就是一个东西。没有任何区别。
>
> 普通servlet 对象注重业务，jsp 则更注重页面展示

**在 jsp 文件中编写Java代码时就相当于在 Servlet 中的 service 方法中编写代码**

#### jsp语法

```jsp
<%
 ... 这里的代码会被放到servlet 的service方法中渲染
%>

<%!
 ... 这里的代码会被放置到servlet 的类体中   
 %>

<%=xxx%> 翻译成 out.print(xxx);

xzxz  普通字符串会被翻译到out.print("xzxz") 参数中
```

#### 内置对象

> 只能在 <%%> service方法中使用

1. out   输出流对象



### session 会话机制（B/S系统结构）

> 用户从打开浏览器到关闭浏览器就是一次会话。
>
> 为啥需要session来保存会话状态？因为Http协议是一种无状态协议
>
> session 对象是存储在服务端的，一个session对应一个请求对象。
>
> session的销毁是通过设置过期时间，就是登录后你的最后一次操作后超过多长时间没有操作，后端会判断这个会话过期



```java
// 只获取 session
HttpSession session = req.getSession(fasle);
// 获取不到创建一个
HttpSession session = req.getSession();
// 向会话中绑定数据
session.setAttribute();
// 从会话域中取数据
Object obj = session.getAttribute();
// 将session对象响应到浏览器
response.setContentType("text/html;charset=UTF-8");

```

#### 为啥不用 request,servletContext 存储？

> 作用域大小：request < session < servletContext
>
> request 对象每次请求都会创建新的对象
>
> servletContext 是所有用户共享的

#### session的实现原理

> ​	在web服务器中有一个session列表。类似于map集合。这个map集合的key存储的是sessionid这个map集合的value存储的是对应的session对象
>
> ​	用户发送第一次请求的时候: 服务器会创建一个新的session对象，同时给session对象生成一个id，然后web服务器会将session的id发送给浏览器，浏览器将session的id保存在浏览器的缓存中
>
> ​	用户发送第二次请求的时候: 会自动将浏览器缓存中的sessionid自动发送给服务器，服务器获取到sessionid然后从session列表中查找到对应的session对象

![image-20230214084004466](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230214084004466.png)

#### 为啥关闭浏览器，会话结束？

> 关闭浏览器后，浏览器中保存的sessionID小艾是，下次重新打开后缓存中没有这个sessionID，自然在服务端找不到对应的session对象

#### web.xml 设置session 过期时间

```xml
<session-config>
	<session-timeout>30</session-timeout>
</session-config>
```

#### cookie禁止了，怎么传递sessionID？

> 可以使用URL重写机制:http://xxxx.com;jsessionid=sessionID
>
> 但是成本高：所以企业常用策略是：你爱用不用



### cookie

```java
// 创建
Cookie cookie = new Cookie("name", "515616");
// 设置1小时后过期
cookie.setMaxage(60*60);
// 设置过期时间0，表示删除次cookie
cookie.setMaxage(0);
// 关联路径,此路径才提交cookie
cookie.setPath();
// 发送到浏览器
res.addCookie(cookie);
```

```java
// 服务器获取cookie
Cookie[]  cookies = req.getCookies();
if (cookies != null) {
    for (Cookie cookie: cookies) {
        System.out.println("cookie = " + cookie.getName() + cookie.getValue());
    }
}
```

#### 10天免登录

```java
// 创建Cookie对象存储用户名
Cookie cookie1 = new Cookie( name: "username", username);
// 创建Cookie对象存储密码
Cookie cookie2 = new Cookie( name:"password"，password); 
// 真实情况下是加密的// 设置cookie的有效期为十天
cookie1.setMaxAge(60 * 60 * 24 * 10):
cookie2.setMaxAge(60 * 60 *24 * 10);
// 设置cookie的path (只要访问这个应用，浏览器就一定要携带这两个cookie)
cookie1.setPath(request.getContextPath()):
cookie2.setPath(request.getContextPath()):
// 响应cookie给浏览器
response.addCookie(cookie1);
response.addCookie(cookie2):
```



### 过滤器

> 就是说我们项目中很多模块都是需要验证登录的，每次执行对应servlet时，先获取session，cookie经行校验然后根据情况经行跳转。所以说我么给能不能把校验这一块提取成共用的方式

编写过滤器

> Filter默认情况下，在服务器启动时就会新建对象

```java
// 注册，匹配/list 路径 @WebFilter("/user/*") 可以使用模糊匹配
@WebFilter("/list")
public class FilterLogin implements Filter {
    // 第一次创建之后调用，只执行一侧
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }
    // 每次请求都会拦截
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("拦截");

        // 放行
        filterChain.doFilter(req, res);
    }
    // 销毁时执行一次
    @Override
    public void destroy() {
        Filter.super.destroy();
    }
}

```

```xml
 <filter>
    <filter-name>FilterLogin</filter-name>
    <filter-class>com.javaweb.servlet.FilterLogin</filter-class>
</filter>
<filter-mapping>
    <filter-name>FilterLogin</filter-name>
    <url-pattern>/list</url-pattern>
</filter-mapping>
```

**执行顺序**

> 在xml文件中配置，则从上到下执行，使用注解方式，则比较类名。如：Filter1,Filter2,先执行Filter1. FilterA,FilterB先执行FilterB

责任链模式：可以在配置文件中更改顺序，来改变执行。不需要改Java代码



### 监听器

> Servlet 留给Java 程序员的特殊时机.
>
> 监听器的方法不需要程序员主动调用，而是被web服务器自动调用

- Jakarta.servlet
	- ServletContextListener
	- ServletContextAttributeListener
	- ServletRequestListener
	- ServletRequestListener
- jakata.servlet.http
	- HttpSessionListener
	- HttpSessionAttributeListener
	- HttpSessionBindingListener
	- HttpSessionIdListener
	- HttpSessionActivetionListener



创建一个监听器

```java
public class MyServletContextListener implements ServletContextListener {
    // servletContext  对象被创建时调用
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContextListener.super.contextInitialized(sce);
    }
    // servletContext 对象被销毁时执行
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        ServletContextListener.super.contextDestroyed(sce);
    }
}
```

这里可以使用配置文件或注解 @WebListener

```xml
<listener>
    <listener-class>com.javaweb.servlet.MyServletContextListener</listener-class>
</listener>
```

