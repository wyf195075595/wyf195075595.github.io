---
title: springboot基础
date: 2023-03-02 16:40:00
tags: springboot基础
categories: java配置
---

### 配置容器

- xml配置容器

	就是spring 的 xml文件中那样的写法

- javaConfig配置容器

	采用java类的方式来配置

	```java
	public class SpringConfig {
	    /*
	    	创建方法，方法的返回值是对象。在方法上加上@Bean
	    	方法的返回值对象就注入到容器中。
	    */
	}
	```

	