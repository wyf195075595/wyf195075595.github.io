---
title: redis 基础
date: 2023-02-22 16:00:00
tags: 数据库
categories: java
---

> 非关系型数据库。

缓存数据分类：

1. 实时同步数据：要求缓存中数据与DB中欧给一致

	如何保证一致？只要DB数据发送改变，清除缓存

2. 阶段性同步：没必要与DB实时同步，大差不差就行

	如何实现？设置缓存数据得生存时长

<!--more-->

### 配置文件

> redis.conf, 运行时指定配置文件(从当前目录)
>
> 配置项查看文档[中文文档](https://www.redis.net.cn/tutorial/3504.html)

```
redis-server /redis.conf
```

连接到主机为 127.0.0.1，端口为 6379 ，密码为 mypass 的 redis 服务上

```
redis-cli -h 127.0.0.1 -p 6379 -a "mypass"
```



命令行方式 查看/配置 配置文件

```
CONFIG GET key
CONFIG SET key value
```



### 数据类型

> Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。

1. 字符串

	string类型是Redis最基本的数据类型，一个键最大能存储512MB。

	```
	SET name zs
	GET name
	```

	

2. Hash

	是一个string类型的field和value的映射表，hash特别适合用于存储对象。**HMSET, HEGTALL** 命令

	```
	HMSET user:1 username redis.net.cn password redis.net.cn points 200
	HGETALL user:1
	```

	

3. List

	列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。

	```
	lpush redis.net.cn rabitmq
	lrange redis.net.cn 0 10
	```

	

4. Set

	Set是string类型的无序集合。通过hash表实现的，添加一个string元素到,key对应的set集合中，成功返回1,如果元素以及在集合中返回0,key对应的set不存在返回错误。

	```
	sadd key member
	smembers key
	```

	

5. zset

	zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。

	```
	zadd key score member
	ZRANGEBYSCORE key 0 1000
	```

	