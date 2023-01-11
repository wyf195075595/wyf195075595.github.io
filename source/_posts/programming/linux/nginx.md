---
title: nginx 基础知识
date: 2022-06-17 18:23:10
tags: nginx
categories: linux
---
> nginx是一款自由的、开源的、高性能的HTTP服务器和反向代理服务器；同时也是一个IMAP、POP3、SMTP代理服务器；nginx可以作为一个HTTP服务器进行网站的发布处理，另外nginx可以作为反向代理进行负载均衡的实现。

<!--more-->

## 1、反/正向代理

> （1）正向代理  （代理客户端）即我们所说的”翻墙“、”梯子“。
>
> ​	特点：我们访问的目标服务求是明确的 ，但目标服务器不清楚具体那个客户端访问了它，它只能看到代理服务器的地址
>
> 客户端需要配置代理
>
> （2）反向代理  （代理服务器）
>
> ​    特点：代理服务器 代理 业务服务器 对客户端隐藏。用户访问的是代理服务器，此过程用户无感。
>
> 客户端不需要配置
>
> ![反向代理](https://raw.githubusercontent.com/wyf195075595/images/main/blog/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy82MTUyNTk1LWFkODZhZjk2OWJiMjg0YTYucG5n)
>
> 

## 2、负载均衡

> 这里提到的客户端发送的、nginx反向代理服务器接收到的请求数量，就是我们说的负载量
>
> 请求数量按照一定的规则进行分发到不同的服务器处理的规则，就是一种均衡规则
>
> 所以~将服务器接收到的请求按照规则分发的过程，称为负载均衡。

## 3、动静分离

> 就是将服务器中的动态资源与静态资源分开部署，需要专门的静态资源服务器

## 4、nginx配置文件

```js
#全局配置  用于进行nginx全局信息的配置

//user用来指定nginx worker进程运行用户以及用户组，默认nobody账号运行
//user  nobody;  

//指定nginx要开启的子进程数量，运行过程中监控每个进程消耗内存(一般几M~几十M不等)根据实际情况进行调整，通常数量是CPU内核数量的整数倍
worker_processes  1; 

//error_log定义错误日志文件的位置及输出级别【debug / info / notice / warn / error / crit】
//error_log  logs/error.log;
//error_log  logs/error.log  notice;
//error_log  logs/error.log  info;

//pid用来指定进程id的存储文件的位置
//pid        logs/nginx.pid;

//worker_rlimit_nofile 1024; worker_rlimit_nofile用于指定一个进程可以打开最多文件数量的描述 ;


# nginx工作模式配置
events {
    //指定最大可以同时接收的连接数量，这里一定要注意，最大连接数量是和worker processes共同决定的。
    worker_connections  1024;

    //multi_accept 配置指定nginx在收到一个新连接通知后尽可能多的接受更多的连接
    //multi_accept on;

    //配置指定了线程轮询的方法，如果是linux2.6+，使用epoll，如果是BSD如Mac请使用Kqueue
    //use epoll;
}

# http设置  用于进行http协议信息的一些配置
http {
    //指定在当前文件中包含另一个文件的指令
    include       mime.types;

    //指定默认处理的文件类型
    default_type  application/octet-stream;

    //log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    //                  '$status $body_bytes_sent "$http_referer" '
    //                  '"$http_user_agent" "$http_x_forwarded_for"';

    //设置存储访问记录的日志
    //access_log  logs/access.log  main;

    //配置on让sendfile发挥作用，将文件的回写过程交给数据缓冲去去完成，而不是放在应用中完成，这样的话在性能提升有有好处
    sendfile        on;

    //让nginx在一个数据包中发送所有的头文件，而不是一个一个单独发
    //tcp_nopush     on;

    //让nginx不要缓存数据，而是一段一段发送，如果数据的传输有实时性的要求的话可以配置它，发送完一小段数据就立刻能得到返回值，但是不要滥用哦
    //tcp_nodelay on;

    //给客户端分配连接超时时间，服务器会在这个时间过后关闭连接。一般设置时间较短，可以让nginx工作持续性更好
    keepalive_timeout  10;

    //设置请求头的超时时间
    //client_header_timeout 10;

    //设置请求体的超时时间
    //client_body_timeout 10;

    //指定客户端响应超时时间，如果客户端两次操作间隔超过这个时间，服务器就会关闭这个链接
    //send_timeout 10;

    //混淆数据，影响三列冲突率，值越大消耗内存越多，散列key冲突率会降低，检索速度更快；值越小key，占用内存较少，冲突率越高，检索速度变慢
    //types_hash_max_size 2048;

    //设置用于保存各种key的共享内存的参数，
    //limit_conn_zone $binary_remote_addr zone=addr:5m ;

    //给定的key设置最大连接数
    //limit_conn addr 100 ;

    //虽然不会让nginx执行速度更快，但是可以在错误页面关闭nginx版本提示，对于网站安全性的提升有好处哦
    // server_tokens off;


    //告诉nginx采用gzip压缩的形式发送数据。这将会减少我们发送的数据量。
    //gzip  on;

    //为指定的客户端禁用gzip功能。我们设置成IE6或者更低版本以使我们的方案能够广泛兼容。
    //gzip_disable "msie6";

    //告诉nginx在压缩资源之前，先查找是否有预先gzip处理过的资源。这要求你预先压缩你的文件（在这个例子中被注释掉了），从而允许你使用最高压缩比，这样nginx就不用再压缩这些文件了
    // gzip_static

    //
    // gzip_vary on;

    //
    // gzip_proxied any;

    //设置对数据启用压缩的最少字节数。如果一个请求小于1000字节，我们最好不要压缩它，因为压缩这些小的数据会降低处理此请求的所有进程的速度。
    //gzip_min_length 1000;

    //设置数据的压缩等级。这个等级可以是1-9之间的任意数值，9是最慢但是压缩比最大的。我们设置为4，这是一个比较折中的设置。
    // gzip_comp_level 6;

    //
    // gzip_buffers 16 8k;

    //
    // gzip_http_version 1.1;

    //设置需要压缩的数据格式。上面例子中已经有一些了，你也可以再添加更多的格式。
    // gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

    # 服务器主机配置  用于进行服务器访问信息的配置
    //一个虚拟主机的配置，一个http中可以配置多个server
    server {
        listen       800;

        //指定ip地址或者域名，多个配置之间用空格分隔
        server_name  localhost;

        //表示整个server虚拟主机内的根目录，所有当前主机中web项目的根目录
        //root        /nginx/www;

        //用户访问web网站时的全局首页
        //index index.php index.html index.html;

        //用于设置www/路径中配置的网页的默认编码格式
        //charset koi8-r;

        //用于指定该虚拟主机服务器中的访问记录日志存放路径
        //access_log  logs/host.access.log  main;

        # 路由配置  用于进行访问路由的配置
        // location / {
        //     //用于指定访问根目录时，访问虚拟主机的web目录
        //     root   html;

        //     //在不指定访问具体资源时，默认展示的资源文件列表
        //     index  index.html index.htm;
        // }
        
        location / {
            proxy_pass http://localhost:8888;
            //下面这两条配置，意思是将http头转发给后端应用，不然你后端应用服务拿客户端IP地址的时候拿到的是nginx代理的地址而不是客户端的。
            proxy_set_header X-real-ip $remote_addr;
            proxy_set_header Host $http_host;
        }
        //error_page  404              /404.html;

        // redirect server error pages to the static page /50x.html
        //
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
	}

}

```

安装nginx

```js
环境准备
1、因为Nginx依赖于gcc的编译环境，所以，需要安装编译环境来使Nginx能够编译起来

yum install gcc-c++cd

2、Nginx的http模块需要使用pcre来解析正则表达式，需要安装pcre。
yum install -y pcre pcre-devel

3、安装依赖的解压包。
yum install -y zlib zlib-devel

4、ssl 功能需要 openssl 库，安装 openssl。
yum install -y openssl openssl-devel

在 /usr/local/ 目录下创建一个nginx文件夹。用来放置nginx安装包并作为安装目录使用

下载Nginx - 下载到我们创建的nginx 文件夹下,或下载到别处然后赋值或移动过来
	-官网下载
        Nginx官方提供了三个类型的版本：
        Mainline version：Mainline 是 Nginx 目前主力在做的版本，可以说是开发版
        Stable version：最新稳定版，生产环境上建议使用的版本
        Legacy versions：遗留的老版本的稳定版
     -命令行下载
        //下载tar包- 
        wget http://nginx.org/download/nginx-1.13.7.tar.gz

     -解压 在 /usr/local/nginx/ 目录下执行
        tar -zxvf nginx-1.13.7.tar.gz
	解压完成时在同一个目录下生成一个同名文件夹，进入该文件夹 cd /nginx-1.13.7


配置安装路径
	./configure --prefix=/usr/local/nginx
编译
	make
安装
	make install
    
配置nginx.conf 

启动
	/usr/local/nginx/sbin/nginx -s reload
    
若想使用外部主机连接上虚拟机访问端口192.168.131.2，需要关闭虚拟机的防火墙：

centOS6及以前版本使用命令： systemctl stop iptables.service

centOS7关闭防火墙命令： systemctl stop firewalld.service


配置软连接（就相当于windows系统中的环境变量，可以在全局任意目录访问）



遇到的问题
	1、rpmdb open failed 的解决办法

今天 yum安装 东西报错

最后就是：
TypeError: rpmdb open failed

这多半是因为rpm数据库出现损坏所致，此错误可能导致多数(甚至是所有的)rpm软件的升级、安装甚至是删除都会出现问题。

解决方法
 cd /var/lib/rpm      # rpmdb所在目录

 rm -f __db.*     # 清除原rpmdb文件

 rpm --rebuilddb     # 重建rpm数据库

 yum clean all     # 清除所有yum的缓存

作者：迷失的指针
链接：https://www.jianshu.com/p/c531e28de195


知识拓展：
	源码的安装一般由3个步骤组成：配置(configure)、编译(make)、安装(makeinstall)。

Configure是一个可执行脚本，它有很多选项，在待安装的源码路径下使用命令./configure–help输出详细的选项列表。

其中--prefix选项是配置安装的路径，如果不配置该选项，安装后可执行文件默认放在/usr/local/bin，库文件默认放在/usr/local/lib，配置文件默认放在/usr/local/etc，其它的资源文件放在/usr/local/share，比较凌乱。

如果配置--prefix，如：

./configure --prefix=/usr/local/test
可以把所有资源文件放在/usr/local/test的路径中，不会杂乱。
用了—prefix选项的另一个好处是卸载软件或移植软件。当某个安装的软件不再需要时，只须简单的删除该安装目录，就可以把软件卸载得干干净净；移植软件只需拷贝整个目录到另外一个机器即可（相同的操作系统）。

当然要卸载程序，也可以在原来的make目录下用一次make uninstall，但前提是make文件指定过uninstall。

```

### 卸载nginx

1. 检查 nginx 服务是否在运行

	```
	ps -ef | grep nginx
	```

	

2. 停止Nginx服务

	```
	// 设置了软连接
	nginx -s stop
	
	// 没有设置软连接
	/usr/sbin/nginx -s stop
	```

	

3. 查找、删除Nginx相关文件

	```
	// 查找nginx 所在文件夹
	
	whereis nginx
	
	// 删除相关文件夹
	
	rm -rf /usr/sbin/nginx
	```

	

4. 再使用yum清理

	```
	yum remove nginx
	```

	

5. 结束

### 安装 nginx  却无法访问

```
关闭firewalld 与iptables防火墙 或开通端口 如果怕不安全开通即可

关闭命令
systemctl stop firewalld    //关闭firewalld防火墙
service iptables stop     //关闭iptables 防火墙  可能提示用 /bin/systemctl stop iptables.service 代替

放通命令
iptables -I INPUT -p tcp --dport 80 -j ACCEPT  //在iptables 放通端口
firewall-cmd --zone =public   --add-port=80/tcp --permanent  //在firewall 放通端口
```



### nginx配置文件讲解

```
Nginx配置文件 nginx.conf 和default.conf 讲解
 

nginx.conf

/etc/nginx/nginx.conf

######Nginx配置文件nginx.conf中文详解#####
 
#定义Nginx运行的用户和用户组
user www www;
 
#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;
  
#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /usr/local/nginx/logs/error.log info;
 
#进程pid文件
pid /usr/local/nginx/logs/nginx.pid;
 
#指定进程可以打开的最大描述符：数目
#工作模式与连接数上限
#这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。
#现在在linux 2.6内核下开启文件打开数为65535，worker_rlimit_nofile就相应应该填写65535。
#这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。
worker_rlimit_nofile 65535;
 
events
{
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型
    #是Linux 2.6以上版本内核中的高性能网络I/O模型，linux建议epoll，如果跑在FreeBSD上面，就用kqueue模型。
    #补充说明：
    #与apache相类，nginx针对不同的操作系统，有不同的事件模型
    #A）标准事件模型
    #Select、poll属于标准事件模型，如果当前系统不存在更有效的方法，nginx会选择select或poll
    #B）高效事件模型
    #Kqueue：使用于FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 MacOS X.使用双处理器的MacOS X系统使用kqueue可能会造成内核崩溃。
    #Epoll：使用于Linux内核2.6版本及以后的系统。
    #/dev/poll：使用于Solaris 7 11/99+，HP/UX 11.22+ (eventport)，IRIX 6.5.15+ 和 Tru64 UNIX 5.1A+。
    #Eventport：使用于Solaris 10。 为了防止出现内核崩溃的问题， 有必要安装安全补丁。
    use epoll;
 
    #单个进程最大连接数（最大连接数=连接数*进程数）
    #根据硬件调整，和前面工作进程配合起来用，尽量大，但是别把cpu跑到100%就行。每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为。
    worker_connections 65535;
 
    #keepalive超时时间。
    keepalive_timeout 60;
 
    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求头的大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。
    #分页大小可以用命令getconf PAGESIZE 取得。
    #[root@web001 ~]# getconf PAGESIZE
    #4096
    #但也有client_header_buffer_size超过4k的情况，但是client_header_buffer_size该值必须设置为“系统分页大小”的整倍数。
    client_header_buffer_size 4k;
 
    #这个将为打开文件指定缓存，默认是没有启用的，max指定缓存数量，建议和打开文件数一致，inactive是指经过多长时间文件没被请求后删除缓存。
    open_file_cache max=65535 inactive=60s;
 
    #这个是指多长时间检查一次缓存的有效信息。
    #语法:open_file_cache_valid time 默认值:open_file_cache_valid 60 使用字段:http, server, location 这个指令指定了何时需要检查open_file_cache中缓存项目的有效信息.
    open_file_cache_valid 80s;
 
    #open_file_cache指令中的inactive参数时间内文件的最少使用次数，如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个文件在inactive时间内一次没被使用，它将被移除。
    #语法:open_file_cache_min_uses number 默认值:open_file_cache_min_uses 1 使用字段:http, server, location  这个指令指定了在open_file_cache指令无效的参数中一定的时间范围内可以使用的最小文件数,如果使用更大的值,文件描述符在cache中总是打开状态.
    open_file_cache_min_uses 1;
     
    #语法:open_file_cache_errors on | off 默认值:open_file_cache_errors off 使用字段:http, server, location 这个指令指定是否在搜索一个文件是记录cache错误.
    open_file_cache_errors on;
}
  
  
  
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http
{
    #文件扩展名与文件类型映射表
    include /etc/nginx/mime.types;
 
    #默认文件类型
    default_type application/octet-stream;
 
    #默认编码
    #charset utf-8;
 
    #服务器名字的hash表大小
    #保存服务器名字的hash表是由指令server_names_hash_max_size 和server_names_hash_bucket_size所控制的。参数hash bucket size总是等于hash表的大小，并且是一路处理器缓存大小的倍数。在减少了在内存中的存取次数后，使在处理器中加速查找hash表键值成为可能。如果hash bucket size等于一路处理器缓存的大小，那么在查找键的时候，最坏的情况下在内存中查找的次数为2。第一次是确定存储单元的地址，第二次是在存储单元中查找键 值。因此，如果Nginx给出需要增大hash max size 或 hash bucket size的提示，那么首要的是增大前一个参数的大小.
    server_names_hash_bucket_size 128;
 
    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求的头部大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。分页大小可以用命令getconf PAGESIZE取得。
    client_header_buffer_size 32k;
 
    #客户请求头缓冲大小。nginx默认会用client_header_buffer_size这个buffer来读取header值，如果header过大，它会使用large_client_header_buffers来读取。
    large_client_header_buffers 4 64k;
 
    #设定通过nginx上传文件的大小
    client_max_body_size 8m;
 
    #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
    #sendfile指令指定 nginx 是否调用sendfile 函数（zero copy 方式）来输出文件，对于普通应用，必须设为on。如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络IO处理速度，降低系统uptime。
    sendfile on;
 
    #开启目录列表访问，合适下载服务器，默认关闭。
    autoindex on;
 
    #此选项允许或禁止使用socke的TCP_CORK的选项，此选项仅在使用sendfile的时候使用
    tcp_nopush on;
      
    tcp_nodelay on;
 
    #长连接超时时间，单位是秒
    keepalive_timeout 120;
 
    #FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
 
    #gzip模块设置
    gzip on; #开启gzip压缩输出
    gzip_min_length 1k;    #最小压缩文件大小
    gzip_buffers 4 16k;    #压缩缓冲区
    gzip_http_version 1.0;    #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    gzip_comp_level 2;    #压缩等级
    gzip_types text/plain application/x-javascript text/css application/xml;    #压缩类型，默认就已经包含textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    gzip_vary on;
 
    #开启限制IP连接数的时候需要使用
    #limit_zone crawler $binary_remote_addr 10m;
 
 
 
    #负载均衡配置
    upstream piao.jd.com {
      
        #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
        server 192.168.80.121:80 weight=3;
        server 192.168.80.122:80 weight=2;
        server 192.168.80.123:80 weight=3;
 
        #nginx的upstream目前支持4种方式的分配
        #1、轮询（默认）
        #每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
        #2、weight
        #指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
        #例如：
        #upstream bakend {
        #    server 192.168.0.14 weight=10;
        #    server 192.168.0.15 weight=10;
        #}
        #2、ip_hash
        #每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
        #例如：
        #upstream bakend {
        #    ip_hash;
        #    server 192.168.0.14:88;
        #    server 192.168.0.15:80;
        #}
        #3、fair（第三方）
        #按后端服务器的响应时间来分配请求，响应时间短的优先分配。
        #upstream backend {
        #    server server1;
        #    server server2;
        #    fair;
        #}
        #4、url_hash（第三方）
        #按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。
        #例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
        #upstream backend {
        #    server squid1:3128;
        #    server squid2:3128;
        #    hash $request_uri;
        #    hash_method crc32;
        #}
 
        #tips:
        #upstream bakend{#定义负载均衡设备的Ip及设备状态}{
        #    ip_hash;
        #    server 127.0.0.1:9090 down;
        #    server 127.0.0.1:8080 weight=2;
        #    server 127.0.0.1:6060;
        #    server 127.0.0.1:7070 backup;
        #}
        #在需要使用负载均衡的server中增加 proxy_pass http://bakend/;
 
        #每个设备的状态设置为:
        #1.down表示单前的server暂时不参与负载
        #2.weight为weight越大，负载的权重就越大。
        #3.max_fails：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream模块定义的错误
        #4.fail_timeout:max_fails次失败后，暂停的时间。
        #5.backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。
 
        #nginx支持同时设置多组的负载均衡，用来给不用的server来使用。
        #client_body_in_file_only设置为On 可以讲client post过来的数据记录到文件中用来做debug
        #client_body_temp_path设置记录文件的目录 可以设置最多3层目录
        #location对URL进行匹配.可以进行重定向或者进行新的代理 负载均衡
    }
　　

default.conf

/etc/nginx/conf.d/default.conf

    server
    {
        #监听端口
        listen 80;
 
        #域名可以有多个，用空格隔开
        server_name www.jd.com jd.com;
        index index.html index.htm index.php;
        root /data/www/jd;
 
        #对******进行负载均衡
        location ~ .*.(php|php5)?$
        {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            include fastcgi.conf;
        }
          
        #图片缓存时间设置
        location ~ .*.(gif|jpg|jpeg|png|bmp|swf)$
        {
            expires 10d;
        }
          
        #JS和CSS缓存时间设置
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
          
        #日志格式设定
        #$remote_addr与$http_x_forwarded_for用以记录客户端的ip地址；
        #$remote_user：用来记录客户端用户名称；
        #$time_local： 用来记录访问时间与时区；
        #$request： 用来记录请求的url与http协议；
        #$status： 用来记录请求状态；成功是200，
        #$body_bytes_sent ：记录发送给客户端文件主体内容大小；
        #$http_referer：用来记录从那个页面链接访问过来的；
        #$http_user_agent：记录客户浏览器的相关信息；
        #通常web服务器放在反向代理的后面，这样就不能获取到客户的IP地址了，通过$remote_add拿到的IP地址是反向代理服务器的iP地址。反向代理服务器在转发请求的http头信息中，可以增加x_forwarded_for信息，用以记录原有客户端的IP地址和原来客户端的请求的服务器地址。
        log_format access '$remote_addr - $remote_user [$time_local] "$request" '
        '$status $body_bytes_sent "$http_referer" '
        '"$http_user_agent" $http_x_forwarded_for';
          
        #定义本虚拟主机的访问日志
        access_log  /usr/local/nginx/logs/host.access.log  main;
        access_log  /usr/local/nginx/logs/host.access.404.log  log404;
          
        #对 "/" 启用反向代理
        location / {
            proxy_pass http://127.0.0.1:88;
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
              
            #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              
            #以下是一些反向代理的配置，可选。
            proxy_set_header Host $host;
 
            #允许客户端请求的最大单文件字节数
            client_max_body_size 10m;
 
            #缓冲区代理缓冲用户端请求的最大字节数，
            #如果把它设置为比较大的数值，例如256k，那么，无论使用firefox还是IE浏览器，来提交任意小于256k的图片，都很正常。如果注释该指令，使用默认的client_body_buffer_size设置，也就是操作系统页面大小的两倍，8k或者16k，问题就出现了。
            #无论使用firefox4.0还是IE8.0，提交一个比较大，200k左右的图片，都返回500 Internal Server Error错误
            client_body_buffer_size 128k;
 
            #表示使nginx阻止HTTP应答代码为400或者更高的应答。
            proxy_intercept_errors on;
 
            #后端服务器连接的超时时间_发起握手等候响应超时时间
            #nginx跟后端服务器连接超时时间(代理连接超时)
            proxy_connect_timeout 90;
 
            #后端服务器数据回传时间(代理发送超时)
            #后端服务器数据回传时间_就是在规定时间之内后端服务器必须传完所有的数据
            proxy_send_timeout 90;
 
            #连接成功后，后端服务器响应时间(代理接收超时)
            #连接成功后_等候后端服务器响应时间_其实已经进入后端的排队之中等候处理（也可以说是后端服务器处理请求的时间）
            proxy_read_timeout 90;
 
            #设置代理服务器（nginx）保存用户头信息的缓冲区大小
            #设置从被代理服务器读取的第一部分应答的缓冲区大小，通常情况下这部分应答中包含一个小的应答头，默认情况下这个值的大小为指令proxy_buffers中指定的一个缓冲区的大小，不过可以将其设置为更小
            proxy_buffer_size 4k;
 
            #proxy_buffers缓冲区，网页平均在32k以下的设置
            #设置用于读取应答（来自被代理服务器）的缓冲区数目和大小，默认情况也为分页大小，根据操作系统的不同可能是4k或者8k
            proxy_buffers 4 32k;
 
            #高负荷下缓冲大小（proxy_buffers*2）
            proxy_busy_buffers_size 64k;
 
            #设置在写入proxy_temp_path时数据的大小，预防一个工作进程在传递文件时阻塞太长
            #设定缓存文件夹大小，大于这个值，将从upstream服务器传
            proxy_temp_file_write_size 64k;
        }
          
          
        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status on;
            access_log on;
            auth_basic "NginxStatus";
            auth_basic_user_file confpasswd;
            #htpasswd文件的内容可以用apache提供的htpasswd工具来产生。
        }
          
        #本地动静分离反向代理配置
        #所有jsp的页面均交由tomcat或resin处理
        location ~ .(jsp|jspx|do)?$ {
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://127.0.0.1:8080;
        }
          
        #所有静态文件由nginx直接读取不经过tomcat或resin
        location ~ .*.(htm|html|gif|jpg|jpeg|png|bmp|swf|ioc|rar|zip|txt|flv|mid|doc|ppt|
        pdf|xls|mp3|wma)$
        {
            expires 15d;
        }
          
        location ~ .*.(js|css)?$
        {
            expires 1h;
        }
    }
}
　　

######Nginx配置文件nginx.conf中文详解#####

#定义Nginx运行的用户和用户组
user www www;

#nginx进程数，建议设置为等于CPU总核心数。
worker_processes 8;
 
#全局错误日志定义类型，[ debug | info | notice | warn | error | crit ]
error_log /usr/local/nginx/logs/error.log info;

#进程pid文件
pid /usr/local/nginx/logs/nginx.pid;

#指定进程可以打开的最大描述符：数目
#工作模式与连接数上限
#这个指令是指当一个nginx进程打开的最多文件描述符数目，理论值应该是最多打开文件数（ulimit -n）与nginx进程数相除，但是nginx分配请求并不是那么均匀，所以最好与ulimit -n 的值保持一致。
#现在在linux 2.6内核下开启文件打开数为65535，worker_rlimit_nofile就相应应该填写65535。
#这是因为nginx调度时分配请求到进程并不是那么的均衡，所以假如填写10240，总并发量达到3-4万时就有进程可能超过10240了，这时会返回502错误。
worker_rlimit_nofile 65535;

events
{
    #参考事件模型，use [ kqueue | rtsig | epoll | /dev/poll | select | poll ]; epoll模型
    #是Linux 2.6以上版本内核中的高性能网络I/O模型，linux建议epoll，如果跑在FreeBSD上面，就用kqueue模型。
    #补充说明：
    #与apache相类，nginx针对不同的操作系统，有不同的事件模型
    #A）标准事件模型
    #Select、poll属于标准事件模型，如果当前系统不存在更有效的方法，nginx会选择select或poll
    #B）高效事件模型
    #Kqueue：使用于FreeBSD 4.1+, OpenBSD 2.9+, NetBSD 2.0 和 MacOS X.使用双处理器的MacOS X系统使用kqueue可能会造成内核崩溃。
    #Epoll：使用于Linux内核2.6版本及以后的系统。
    #/dev/poll：使用于Solaris 7 11/99+，HP/UX 11.22+ (eventport)，IRIX 6.5.15+ 和 Tru64 UNIX 5.1A+。
    #Eventport：使用于Solaris 10。 为了防止出现内核崩溃的问题， 有必要安装安全补丁。
    use epoll;

    #单个进程最大连接数（最大连接数=连接数*进程数）
    #根据硬件调整，和前面工作进程配合起来用，尽量大，但是别把cpu跑到100%就行。每个进程允许的最多连接数，理论上每台nginx服务器的最大连接数为。
    worker_connections 65535;

    #keepalive超时时间。
    keepalive_timeout 60;

    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求头的大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。
    #分页大小可以用命令getconf PAGESIZE 取得。
    #[root@web001 ~]# getconf PAGESIZE
    #4096
    #但也有client_header_buffer_size超过4k的情况，但是client_header_buffer_size该值必须设置为“系统分页大小”的整倍数。
    client_header_buffer_size 4k;

    #这个将为打开文件指定缓存，默认是没有启用的，max指定缓存数量，建议和打开文件数一致，inactive是指经过多长时间文件没被请求后删除缓存。
    open_file_cache max=65535 inactive=60s;

    #这个是指多长时间检查一次缓存的有效信息。
    #语法:open_file_cache_valid time 默认值:open_file_cache_valid 60 使用字段:http, server, location 这个指令指定了何时需要检查open_file_cache中缓存项目的有效信息.
    open_file_cache_valid 80s;

    #open_file_cache指令中的inactive参数时间内文件的最少使用次数，如果超过这个数字，文件描述符一直是在缓存中打开的，如上例，如果有一个文件在inactive时间内一次没被使用，它将被移除。
    #语法:open_file_cache_min_uses number 默认值:open_file_cache_min_uses 1 使用字段:http, server, location  这个指令指定了在open_file_cache指令无效的参数中一定的时间范围内可以使用的最小文件数,如果使用更大的值,文件描述符在cache中总是打开状态.
    open_file_cache_min_uses 1;
    
    #语法:open_file_cache_errors on | off 默认值:open_file_cache_errors off 使用字段:http, server, location 这个指令指定是否在搜索一个文件是记录cache错误.
    open_file_cache_errors on;
}
 
 
 
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http
{
    #文件扩展名与文件类型映射表
    include /etc/nginx/mime.types;

    #默认文件类型
    default_type application/octet-stream;

    #默认编码
    #charset utf-8;

    #服务器名字的hash表大小
    #保存服务器名字的hash表是由指令server_names_hash_max_size 和server_names_hash_bucket_size所控制的。参数hash bucket size总是等于hash表的大小，并且是一路处理器缓存大小的倍数。在减少了在内存中的存取次数后，使在处理器中加速查找hash表键值成为可能。如果hash bucket size等于一路处理器缓存的大小，那么在查找键的时候，最坏的情况下在内存中查找的次数为2。第一次是确定存储单元的地址，第二次是在存储单元中查找键 值。因此，如果Nginx给出需要增大hash max size 或 hash bucket size的提示，那么首要的是增大前一个参数的大小.
    server_names_hash_bucket_size 128;

    #客户端请求头部的缓冲区大小。这个可以根据你的系统分页大小来设置，一般一个请求的头部大小不会超过1k，不过由于一般系统分页都要大于1k，所以这里设置为分页大小。分页大小可以用命令getconf PAGESIZE取得。
    client_header_buffer_size 32k;

    #客户请求头缓冲大小。nginx默认会用client_header_buffer_size这个buffer来读取header值，如果header过大，它会使用large_client_header_buffers来读取。
    large_client_header_buffers 4 64k;

    #设定通过nginx上传文件的大小
    client_max_body_size 8m;

    #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改成off。
    #sendfile指令指定 nginx 是否调用sendfile 函数（zero copy 方式）来输出文件，对于普通应用，必须设为on。如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络IO处理速度，降低系统uptime。
    sendfile on;

    #开启目录列表访问，合适下载服务器，默认关闭。
    autoindex on;

    #此选项允许或禁止使用socke的TCP_CORK的选项，此选项仅在使用sendfile的时候使用
    tcp_nopush on;
     
    tcp_nodelay on;

    #长连接超时时间，单位是秒
    keepalive_timeout 120;

    #FastCGI相关参数是为了改善网站的性能：减少资源占用，提高访问速度。下面参数看字面意思都能理解。
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;

    #gzip模块设置
    gzip on; #开启gzip压缩输出
    gzip_min_length 1k;    #最小压缩文件大小
    gzip_buffers 4 16k;    #压缩缓冲区
    gzip_http_version 1.0;    #压缩版本（默认1.1，前端如果是squid2.5请使用1.0）
    gzip_comp_level 2;    #压缩等级
    gzip_types text/plain application/x-javascript text/css application/xml;    #压缩类型，默认就已经包含textml，所以下面就不用再写了，写上去也不会有问题，但是会有一个warn。
    gzip_vary on;

    #开启限制IP连接数的时候需要使用
    #limit_zone crawler $binary_remote_addr 10m;



    #负载均衡配置
    upstream piao.jd.com {
     
        #upstream的负载均衡，weight是权重，可以根据机器配置定义权重。weigth参数表示权值，权值越高被分配到的几率越大。
        server 192.168.80.121:80 weight=3;
        server 192.168.80.122:80 weight=2;
        server 192.168.80.123:80 weight=3;

        #nginx的upstream目前支持4种方式的分配
        #1、轮询（默认）
        #每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
        #2、weight
        #指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
        #例如：
        #upstream bakend {
        #    server 192.168.0.14 weight=10;
        #    server 192.168.0.15 weight=10;
        #}
        #2、ip_hash
        #每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。
        #例如：
        #upstream bakend {
        #    ip_hash;
        #    server 192.168.0.14:88;
        #    server 192.168.0.15:80;
        #}
        #3、fair（第三方）
        #按后端服务器的响应时间来分配请求，响应时间短的优先分配。
        #upstream backend {
        #    server server1;
        #    server server2;
        #    fair;
        #}
        #4、url_hash（第三方）
        #按访问url的hash结果来分配请求，使每个url定向到同一个后端服务器，后端服务器为缓存时比较有效。
        #例：在upstream中加入hash语句，server语句中不能写入weight等其他的参数，hash_method是使用的hash算法
        #upstream backend {
        #    server squid1:3128;
        #    server squid2:3128;
        #    hash $request_uri;
        #    hash_method crc32;
        #}

        #tips:
        #upstream bakend{#定义负载均衡设备的Ip及设备状态}{
        #    ip_hash;
        #    server 127.0.0.1:9090 down;
        #    server 127.0.0.1:8080 weight=2;
        #    server 127.0.0.1:6060;
        #    server 127.0.0.1:7070 backup;
        #}
        #在需要使用负载均衡的server中增加 proxy_pass http://bakend/;

        #每个设备的状态设置为:
        #1.down表示单前的server暂时不参与负载
        #2.weight为weight越大，负载的权重就越大。
        #3.max_fails：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream模块定义的错误
        #4.fail_timeout:max_fails次失败后，暂停的时间。
        #5.backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。

        #nginx支持同时设置多组的负载均衡，用来给不用的server来使用。
        #client_body_in_file_only设置为On 可以讲client post过来的数据记录到文件中用来做debug
        #client_body_temp_path设置记录文件的目录 可以设置最多3层目录
        #location对URL进行匹配.可以进行重定向或者进行新的代理 负载均衡
    }
```

### 软链接

```
ln的链接分 软链接 和 硬链接 两种：

1、 软链接就是：“ln –s 源文件 目标文件”，只会在选定的位置上生成一个文件的镜像，不会占用磁盘空间，类似与windows的快捷方式。

2、 硬链接ln源文件目标文件，没有参数-s， 会在选定的位置上生成一个和源文件大小相同的文件，无论是软链接还是硬链接，文件都保持同步变化。
```

![](https://raw.githubusercontent.com/wyf195075595/images/main/blog/ruanlink.png)

```
软链接又叫符号链接，
这个文件包含了另一个文件的路径名。
可以是任意文件或目录，
可以链接不同文件系统的文件。

链接文件甚至可以链接不存在的文件，
这就产生一般称之为 ”断链” 的现象，
链接文件甚至可以循环链接自己。
类似于编程语言中的递归。

软链接文件只是其源文件的一个标记，
当删除了源文件后，
链接文件不能独立存在，
虽然仍保留文件名，
但却不能查看软链接文件的内容了。


用ln -s 命令可以生成一个软连接，如下:
ln -s source_file softlink_file
ln -s /usr/local/nginx/sbin/nginx /usr/local/bin
给nignx 设置软连接

在对符号文件进行读或写操作的时候，
系统会自动把该操作转换为对源文件的操作，
但删除链接文件时，系统仅仅删除链接文件，
而不删除源文件本身。

删除硬/软链接用 
rm softlink_file 或 unlink softlink_file
```

### 配置文件实例

```js

user  root;
worker_processes  4;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;
    gzip  on;

    upstream bdxxWeb {
        #server 22.56.142.70:8002 ;
        server 192.168.1.12:8001;
    }

    upstream bdxxAPI {
       #server 22.56.142.70:9002 ;
       server 192.168.1.12:9002;
    }

    server {
        listen    8002;
        #server_name  22.56.131.205;
        server_name  192.168.1.12;
        #charset koi8-r;
        access_log  logs/bdxxWeb.access.log  main;
        location / {
            #proxy_pass http://bdxxWeb;
            root  /home/jysp/workspace/html/;
            index  index.html index.htm;
        }
        location /api/ {
            proxy_pass http://bdxxAPI/;
            index  index.html index.htm;
            proxy_set_header Host $host;
            proxy_set_header X-Real-ip $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size    1000m;
        } 

    }

    server {
        listen   9003;
        server_name  192.168.1.12;
        #charset koi8-r;
        access_log  logs/bdxxAPI.access.log  main;
        location / {
            proxy_pass http://bdxxAPI;
            index  index.html index.htm;
            proxy_set_header Host $host;
            proxy_set_header X-Real-ip $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size    1000m;
        } 
    }

    server {
        listen    18003;
        #server_name  22.56.131.205;
        server_name  192.168.1.12;
        #charset koi8-r;
        access_log  logs/bdxxWeb.access.log  main;
        location / {
            proxy_pass http://bdxxWeb;                        
            index  index.html index.htm;
        }
        location /api/ {
            proxy_pass http://bdxxAPI/;
            index  index.html index.htm;
            proxy_set_header Host $host;
            proxy_set_header X-Real-ip $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            client_max_body_size    1000m;       
        } 
    }


    server {
        listen    18002;
        server_name  192.168.1.12;
        #charset koi8-r;
        access_log  logs/webnew.access.log  main;
        location / {
            root  /home/jysp/workspace/newweb/;
            index  index.html index.htm;
        }
            
    }


}

```

```
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    server {
        listen 80;
        location / {
            proxy_pass    http://106.54.247.250:8001;
        }
    }
    server {
        listen       8001;
        server_name  106.54.247.250;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /projects/koatest/public/;
            index  index.html index.htm;
        }

        location /prod-api/ {
            #所有对后端的请求加一个/prod-api/前缀方便区分，真正访问的时候移除替换这个前缀
            rewrite ^/prod-api/(.*)$ /api/$1 break;
            #将真正的请求代理到serverA,即真实的服务器地址，ajax的url为/prod-api/user/1的请求将会访问http://ihrm-java.itheima.net/api/user/1
            proxy_pass http://ihrm-java.itheima.net;
        }

        #404页面
        error_page  404              /404.html;

        #错误页面
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

