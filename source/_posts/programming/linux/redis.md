---
title: Linux 安装 redis
date: 2023-03-09 08:23:10
tags: redis
categories: linux
---

### linux 安装 redis

1、去[官网](redis.io)下载 redis ,上传解压。

然后会发现 文件夹中有 MakeFile 文件，这是编译所需文件

2、编译

```
make
```

3、安装

```
make install
```

> 安装后得命令在 /usr/local/bin 目录下