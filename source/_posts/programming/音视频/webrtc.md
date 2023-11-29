---
title: webrtc 基础
date: 2023-11-08 14:23:10
tags: webrtc
categories: 音视频
---

> WebRTC是一项实时通讯技术，它允许网络应用或者站点，在不借助中间媒介的情况下，建立浏览器之间点对点的（Peer-to-Peer）的连接，实现视频流和（或）音频流或者其他任意数据的传输。它包括的这些标准使用户在无需安装任何插件或者第三方的软件的情况下，创建点对点（Peer-to-Peer）的数据分享和电话会议成为可能。

## 理论知识

### **媒体协商（sdp）**

​	彼此了解对方支持的媒体格式，这是两个不同网络环境浏览器要实现语音视频通话的首要解决问题

### **网络协商（candidate）**

​	了解对方的网络情况

1) 获取外网IP地址映射

2) 通信服务器交换网络信息

实际情况是：我们电脑和电脑之间或大或小都是在某个局域网里面，需要NAT(网络地址转换)

#### **STUN**

​	外网映射通信（内网穿透）

STUN是一种网络协议，它允许NAT后的客户端找出自己的公网地址，查出自己位于哪种类型的NAT为某一个本端口绑定的Internel（外网）端端口，外网就可以通过UDP通信。

#### **TURN**

​	中继服务通信

如果上述外网映射通信不通的话，这时候需要公网作为一个中继，对来网的数据转发，这个转发的协议被定义为TURN

服务网络（200kbps单向通信一对一带宽为良好的通信）

### 信令服务器

> 两个不同网络环境下的应用程序可以通过信令服务器交换信息，如websocket ,信令服务器一般都是部署到公网，它要求能被两端的应用程序访问到



## Linux 安装 coturn

> [参考链接](https://blog.csdn.net/lepaitianshi/article/details/123778506)

1. 下载源码

	```shell
	git clone https://github.com/coturn/coturn.git
	```

2. 下载相关库文件(安装依赖)

	```shell
	sudo apt-get install libssl-dev libevent-dev
	```

3. 进入源码目录，编译

	```shell
	./configure --prefix=/usr/local/coturn
	```

4. 安装

	```shell
	make -j 4 
	sudo make install
	```

5. 配置环境变量

	```shell
	export coturn_home=/usr/local/coturn
	export PATH=$PATH:$coturn_home/bin
	```

6. 测试

	```shell
	    sudo nohup turnserver -L 0.0.0.0 -a -u lqf:123456 -v -f -r > /usr/local/coturn/logs &
	```

	