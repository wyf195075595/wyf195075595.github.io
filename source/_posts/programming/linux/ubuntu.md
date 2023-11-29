---
title: 虚拟机安装Ubuntu ssh 连接
date: 2023-11-01 10:23:10
tags: ubuntu
categories: linux
---

> Ubuntu 与 conteos 最大区别就是 Ubuntu 安装后自带操作界面。很流畅，虽然conteos 也能安装界面但是不好用，卡的一批。然后就是包管理工具，编辑 等一些命令不一样

下载好 Ubuntu ISO镜像后，在虚拟机上安装好。登录后发现 连接工具 无法访问 Ubuntu

​	ubuntu18.04中只默认安装了openssh-client,所以如果想用ssh远程登录ubuntu，需要下载安装openssh-server，并启动ssh服务

<!--more-->

### 安装 openssh-server

```shell
sudo apt-get install openssh-server
```

#### 编辑配置文件 sshd_config

```shell
sudo vi /etc/ssh/sshd_config
```

找到：PermitRootLogin prohibit-password 禁用

添加：PermitRootLogin yes

配置重启

```shell
sudo service ssh restart
```

#### 防火墙管理

安装 ufw 系统自带的防火墙管理

```shell
sudo apt-get install ufw
```

常用命令

- 启用/关闭

```shell
sudo ufw enable(开启)/disable(关闭)
sudo ufw default deny
```

- 打开或关闭某个端口打开或关闭某个端口

```shell
sudo ufw allow smtp　//允许所有的外部IP访问本机的25/tcp (smtp)端口
sudo ufw allow 22/tcp //允许所有的外部IP访问本机的22/tcp (ssh)端口
sudo ufw allow 53 //允许外部访问53端口(tcp/udp)
sudo ufw allow from 192.168.1.100 //允许此IP访问所有的本机端口
sudo ufw allow proto tcp from 192.168.0.1 to 192.168.0.2 port 22 //允许192.168.0.1访问192.168.0.2的22/tcp（ssh）端口
sudo ufw deny smtp //禁止外部访问smtp服务
sudo ufw delete allow smtp //删除上面建立的某条规则
```

- 查看防火墙状态

	```shell
	sudo ufw status
	```

一般用户，只需如下设置：

```shell
sudo apt-get install ufw
sudo ufw enable
sudo ufw default deny
```

以上三条命令已经足够安全了，如果你需要开放某些服务，再使用sudo ufw allow开启。

### vi 常用命令

> 进入vi之后，是处于「命令行模式（command mode）」，您要切换到「插入模式（Insert mode）」才能够输入文字。初次使用vi的人都会想先用上下左右键移动光标，结果电脑一直哔哔叫，把自己气个半死，所以进入vi后，先不要乱动，转换到「插入模式（Insert mode）」再说吧！

#### 插入模式

> ESC 可从插入模式回到命令模式

1. i	光标当前位置插入
2. a   光标位置后面追加
3. o   换行追加，插入新的一行

#### 保存退出

在命令模式下输入 : 开头

- 「w」：在冒号输入字母「w」就可以将文件保存起来。
- 「 E」 离开vi
- 「q」：按「q」就是退出，如果无法离开vi，可以在「q」后跟一个「!」强制离开vi。
- 「qw」：一般建议离开时，搭配「w」一起使用，这样在退出的时候还可以保存文件。

#### 移动光标

> vi可以直接用键盘上的光标来上下左右移动，但正规的vi是用小写英文字母「h」、「j」、「k」、「l」，分别控制光标左、下、上、右移一格。

- 按「ctrl」+「b」：屏幕往“后”移动一页。
- 按「ctrl」+「f」：屏幕往“前”移动一页。
- 按「ctrl」+「u」：屏幕往“后”移动半页。
- 按「ctrl」+「d」：屏幕往“前”移动半页。
- 按数字「0」：移到文章的开头。
- 按「G」：移动到文章的最后。
- 按「$」：移动到光标所在行的“行尾”。
- 按「^」：移动到光标所在行的“行首”
- 按「w」：光标跳到下个字的开头
- 按「e」：光标跳到下个字的字尾
- 按「b」：光标回到上个字的开头
- 按「#l」：光标移到该行的第#个位置，如：5l,56l

#### 删除

- 「x」：每按一次，删除光标所在位置的“后面”一个字符。
- 「#x」：例如，「6x」表示删除光标所在位置的“后面”6个字符。
- 「X」：大写的X，每按一次，删除光标所在位置的“前面”一个字符。
- 「#X」：例如，「20X」表示删除光标所在位置的“前面”20个字符。
- 「dd」：删除光标所在行。
- 「#dd」：从光标所在行开始删除#行

#### 复制

- 「yw」：将光标所在之处到字尾的字符复制到缓冲区中。
- 「#yw」：复制#个字到缓冲区
- 「yy」：复制光标所在行到缓冲区。
- 「#yy」：例如，「6yy」表示拷贝从光标所在的该行“往下数”6行文字。
- 「p」：将缓冲区内的字符贴到光标所在位置。注意：所有与“y”有关的复制命令都必须与“p”配合才能完成复制与粘贴功能。

#### 替换

- 「r」：替换光标所在处的字符。
- 「R」：替换光标所到之处的字符，直到按下「ESC」键为止

#### 恢复/撤消/还原上一次操作

「u」：如果误执行一个命令，可以马上按下「u」，撤消上一个操作。按多次“u”可以执行多次撤消。

#### 更改

- 「cw」：更改光标所在处的字到字尾处
- 「c#w」：例如，「c3w」表示更改3个字

#### 跳至指定的行

- 「ctrl」+「g」列出光标所在行的行号。
- 「#G」：例如，「15G」，表示移动光标至文章的第15行行首。

#### 列出行号

「set nu」：输入「set nu」后，会在文件中的每一行前面列出行号。

#### 跳到文件中的某一行

「#」：「#」号表示一个数字，在冒号后输入一个数字，再按回车键就会跳到该行了，如输入数字15，再回车，就会跳到文章的第15行。

#### 查找字符

- 「/关键字」：先按「/」键，再输入您想寻找的字符，如果第一次找的关键字不是您想要的，可以一直按「n」会往后寻找到您要的关键字为止。
- 「?关键字」：先按「?」键，再输入您想寻找的字符，如果第一次找的关键字不是您想要的，可以一直按「n」会往前寻找到您要的关键字为止。

#### vi命令列表

- h	左移光标一个字符
- l	右移光标一个字符
- k	光标上移一行
- j	光标下移一行
- ^	光标移动至行首
- 0	数字“0”，光标移至文章的开头
- G	光标移至文章的最后
- $	光标移动至行尾
- Ctrl+f	向前翻屏
- Ctrl+b	向后翻屏
- Ctrl+d	向前翻半屏
- Ctrl+u	向后翻半屏
- wq filename	储存正在编辑的文件为filename，并退出vi



### 安装 ffmpeg

> [参考链接](https://www.myfreax.com/how-to-install-ffmpeg-on-ubuntu-18-04/)

ubuntu 18.04 版本 安装 4.x版本 ffmpeg

```shell
sudo add-apt-repository ppa:jonathonf/ffmpeg-4

sudo apt install ffmpeg

// 下载到多少，突然报网络问题断联，补全下载
sudo apt install ffmpeg --prefix
```

如果安装ppa 失败，那么要安装这个

```shell
sudo apt install software-properties-common
```



### 安装nodejs

> 默认安装的 nodejs 是8版本，连npm都不带，不大行。
>
> [查询nodejs版本](https://nodejs.org/dist/),下载 linux-x64.tar.gz 后缀文件

```shell
wget https://nodejs.org/dist/v10.16.0/node-v10.16.0-linux-x64.tar.gz
```

安装后解压，放到 /usr/local/nodejs-xxx 目录

```
tar -zxvf nodejs-xxx 
```

### 安装 PM2

> PM2是node进程管理工具，可以利用它来简化很多node应用管理的繁琐任务，如性能监控、自动重启、负载均衡等，而且使用非常简单。

```
npm i pm2 -g
```



### 设置全局变量

1. 临时设置

	```shell
	export PATH=/home/yan/share/usr/local/arm/3.4.1/bin:$PATH
	```

2. 当前用户全局

	打开 ~/.bashrc,添加下面配置

	```shell
	export PATH=/home/yan/share/usr/local/arm/3.4.1/bin:$PATH
	```

3. 所有用户的全局配置

	修改 /etc/profile

	```
	export PATH=/home/yan/share/usr/local/arm/3.4.1/bin:$PATH
	```

4. 配置修改生效

	```shell
	source profile
	```


### mkcert.exe

> 自己给自己签发一个ssl证书,可用于本地生成 ssl 证书
>
> [ubuntu 上使用](https://blog.csdn.net/weixin_44692055/article/details/131489771)

1. [下载](https://dl.filippo.io/mkcert/latest?for=linux/amd64)

2. 将下载二进制文件移动至系统路径

	```shell
	mv mkcert-v1.4.3-linux-amd64 /usr/bin/mkcert
	```

3. 设置执行权限

	```shell
	chmod +x /usr/bin/mkcert
	```

4. 验证 Mkcert 版本

	```shell
	mkcert --version
	```

5. 生成本地 CA 证书

	```shell
	mkcert -install
	
	# 查看证书路径
	mkcert -CAROOT
	
	```

6. 为本地托管的网站生成证书和密钥文件

	生成的文件在执行命令目录下

	```shell
	mkcert localhost
	```

7. 在nginx 中使用证书

	```nginx
	server {
	   listen       443 ssl;
	   server_name  localhost;
	
	   ssl_certificate    /home/ubuntu/.local/share/mkcert/localhost.pem;
	   ssl_certificate_key /home/ubuntu/.local/share/mkcert/localhost-key.pem;
	
	   ssl_session_cache    shared:SSL:1m;
	   ssl_session_timeout  5m;
	
	   ssl_ciphers  HIGH:!aNULL:!MD5;
	   ssl_prefer_server_ciphers  on;
	
	   location / {
	       root   html;
	       index  index.html index.htm;
	   }
	}
	```

8. 重启 nginx 访问地址



### **安装软件失败**

> Could not get lock /var/lib/dpkg/lock-frontend - open (11: Resource temporarily unavailable
>
> [参考](https://blog.csdn.net/weixin_41712499/article/details/130681042)

这个错误通常意味着有另一个进程正在使用apt命令或dpkg命令，因此无法获得对dpkg锁的访问权限。以下是解决步骤：

1. 确认是否有其他apt或dpkg进程正在运行。可以使用以下命令：

	```
	ps aux | grep -i apt
	ps aux | grep -i dpkg
	12
	```

	如果有其他进程正在运行，请等待它完成并退出。或者杀掉进程

2. 如果没有其他进程正在运行，则需要清理锁定文件。可以使用以下命令：

	```
	sudo rm /var/lib/dpkg/lock-frontend
	sudo rm /var/lib/dpkg/lock
	
	```

	这将删除apt和dpkg使用的所有锁定文件。

3. 然后，强制重新配置dpkg数据库：

	```
	sudo dpkg --configure -a
	
	```

4. 最后，更新软件包列表：

	```
	sudo apt update
	```
	

然后应该就可以安装或更新软件包了。

