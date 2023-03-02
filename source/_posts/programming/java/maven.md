---
title: maven基础
date: 2023-02-14 11:20:00
tags: maven基础
categories: maven
---

### 项目管理器，可以帮我们做什么？

> - 管理jar 文件
> - 自动下载jar和他的文档源码
> - 管理jar直接的依赖，a.jar 依赖b.jar 他会自动下载b.jar
> - 帮你管理jar的版本
> - 帮你编译程序： java-> class
> - 帮你测试代码
> - 帮你打包，形成jar/war 文件
> - 帮你部署项目

### 核心概念

1. POM

	把一个项目当作模型使用。控制maven构建项目的过程

2. 约定的目录结构

	maven项目的目录和文件和位置都是规范的

3. 依赖仓库

	管理项目可以使用的jar

4. 仓库管理

	资源存放的位置

5. 生命周期

6. 插件和木比爱哦

7. 继承

8. 聚合



### 修改本机资源存放目录位置

修改maven的配置文件，maven的安装目录/conf/settings.xml

为啥要改？因为默认放在C盘

```xaml
<localRepository>D://maven//localSpace</localRepository>
```

<!--more-->

### 仓库

> 本地仓库：就是存放maven使用的jar和我们项目使用的jar
>
> 远程仓库：
>
> 中央仓库【mvenrepository.com】：所有开发人员共享的	集中仓库，为了避免服务器压力过大，出现了很多镜像仓库，分布在世界各个位置。我们最好访问离自己近的仓库，访问会比较快
>
> 私服：
>
> 在公司内部，局域网中使用，不对外使用

### pom

> 项目对象模型

1. 坐标：唯一值，标识项目唯一

	```xml
	<groupId>公司域名倒写</groupId>
	<artifactId>自定义项目名称</artifactId>
	<version>自定义版本号</version>
	```

2. dependencies 依赖

	```xml
	// 里面存放所有依赖
	<dependencies>
		<dependency>
	    	<groupId>公司域名倒写</groupId>
	        <artifactId>自定义项目名称</artifactId>
	        <version>自定义版本号</version>
	    </dependency>
	    <dependency>
	    	<groupId>mysql</groupId>
	        <artifactId>mysql-connector-java</artifactId>
	        <version>5.1.9</version>
	    </dependency>
	
	</dependencies>
	```

3. properties: 设置属性

4. build: 构建相关



### 相关命令

1. mvn clean 清理打包文件
2. mvn package  打包
3. mvn install   安装主程序
4. mvn deploy  部署主程序
5. mvn compile  编译主程序



### 编译插件配置

```xml
<build>
	<plugins>
    	<groupId>org.apache.maven.plugins</groupId>
        // 插件名
        <artifactId>maven-compiler-plugin</artifactId>
        // 插件版本
        <version>3.8.1</version>
        <configuration>
            // jdk1.8 环境写的代码
        	<source>1.8</source>
            // 运行环境 jdk 1.8
            <target>1.8</target>
        </configuration>
    </plugins>
</build>
```

