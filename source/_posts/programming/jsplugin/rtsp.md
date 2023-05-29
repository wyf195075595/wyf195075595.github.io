---
title: h5播放rtsp格式视频
date: 2023-03-27 18:53:10
tags: rtsp
categories: js
---

> [Linux下载和安装yasm、FFmpeg、流媒体服务SRS4.0](https://www.jianshu.com/p/e16a453bbfcc)
>
> [FFmpeg下载](http://www.ffmpeg.org/releases/)

### 方案

前端：

- jsmpeg.js (连接wbesocket，绘制视频图像)

服务端（node）：

- express（静态服务，接口处理）
- body-parser（处理接口接收数据）
- node-rtsp-stream-jsmpeg （下发FFmpeg指令，创建websocke服务端）
- child_process（启动shell脚本指令）

其他：

- FFmpeg(视频流转码)
- pm2(node进程守护)
- 云台控制程序（通过视频厂家提供的sdk二次开发，通过ajax调用）

> [参考链接](https://juejin.cn/post/6844903949309313037)

-----------------------------------------------------------------------------------------------

<!--more-->

### zlmediakit搭建和配合ffmpeg使用

> [zlmediakit](https://github.com/ZLMediaKit/ZLMediaKit) 是一个基于C++11的高性能运营级流媒体服务框架，可以推送各种视频，音频流。
>
> ffmpeg 推流视频，zlmediakit 流媒体服务器经行转发，转发后的地址有多种视频格式

- zlmediakit linux 环境搭建：
	[这篇文章](https://zhuanlan.zhihu.com/p/366774975)写得已经非常详细了，就不再拾人牙慧了
- ffmpeg安装：
	虽然上面的搭建文档说不用安装ffmpeg，但是如果使用命令的话，肯定还是要安装的(不安装指的是在项目中调用api)，这种方式只能安装低版本，高版本参考上面
	1).sudo apt-get update
	2).sudo apt-get install ffmpeg
- 测试(；推流路径格式是：rtsp://ip/…（后面可随意命名）)：
	例：把test文件推到某个rtsp地址上去
	ffmpeg -re -i test.mp4 -vcodec h264 -acodec aac -f rtsp -rtsp_transport tcp rtsp://ip/live/test
	注意事项：
	1）要推的视频路径写绝对路径；
	2）推流地址格式为: rtsp://ip/…（后面可随意定义）
	3）zlmediakit的默认路径是554，如果改了端口号，ip后要加上。
	4）如果使用docker安装zlmediakit，那么要注意映射的端口不是554的话，端口号也得写上
- [附带推流测试地址(附带右边列表很多相关资料)：](https://github.com/ZLMediaKit/ZLMediaKit/wiki/ZLMediaKit推流测试)

> [参考地址](https://blog.csdn.net/weixin_43248279/article/details/124734505)

[ZIMediaKit源地址](https://qiteecom/xia-chu/ZIMediaKit)

```shell
// rtsp推流(文件推流)
ffmpeg -re -i test.mp4 -rtsp transport tcp -c copy -f rtsp rtsp://127.0.0.1:554/live/test
// rtsp推流(文件循环推流)
ffmpeg -re -stream_loop -1 -i test.mp4 -rtsp_transport tcp -c copy -f rtsp rtsp://127.0.0.1:554/live/test
// rtmp推流(文件推流)
Ffmpeg -re -i test.mp4 -vcodec h264_nvenc -acodec aac -f flv rtmp://127.0.0.1:1935/live/test
// rtmp推流(文件循环推流)
ffmpeg -re -stream_loop -1 -i test.mp4 -vcodec h264 -acodec aac -f flv rtmp://127.0.0.1:1935/live/test

// 推流笔记本摄像头
ffmpeg -i /dev/video0 -vcodec libx264 -acodec copy -preset:v ultrafast -tune:v zerolatency -f flv rtmp://192.168.94.128:1935/live/test


# ZIMediaKit支持多种流媒体协议的转换，协议转换后的播放地址
// rtsp播放
rtsp://127.0.0.1:554/live/test

//rtmp播放
rtmp://127.0.0.1:1935/live/test
	
// hls播放
http://127.0.0.1:80/live/test/hls.m3u8

// http-flv播放
http://127.0.0.1:80/live/test.live.flv

// http-ts播放
http://127.0.0.1:80/live/test.live.ts
```

> [ffplay ffmpeg快速检验摄像头](https://blog.csdn.net/qq_37429313/article/details/116201062)
>
> [ZLMediaKit 推/拉流](https://blog.csdn.net/YellowShite/article/details/120367738)

### ffmpeg 基础

> Fmpeg 是领先的多媒体框架，能够解码、编码、转码、混合、解密、流媒体、过滤和播放人类和机器创造的几乎所有东西。它支持最晦涩的古老格式，直到最尖端的格式。
>
> 简单来说可以将浏览器不支持的流转换为支持的流供页面展示.
>
> 除了视频文件，还能处理 摄像头，声卡，网络直播，电脑桌面进行数据采集

```shell
# -i 表示输入	a.mov
# -c 表示为输出文件指定编码格式
# copy 表示从输入文件a.mov中直接拷贝音频和视频数据
# 将输入的 a.mov 转化为 mp4 格式 输出 a.mp4
ffmpeg -i a.mov -c copy a.mp4


# -c:v  code video 指定视频编码
# -c:a  code audio 指定音频编码
# 将 video.avi 输出为 视频编码246，音频编码 acc 格式 的mp4文件
ffmpeg -i video.avi -c:v h246 -c:a aac video.mp4

# 查看支持的格式
ffmpeg -c

# 常用参数选项
ffmpeg -h

# 显示一些高级参数
ffmpeg -h -long

# 显示更多
ffmpeg -h full

# 输出参数

-r 24 # 指定输出视频帧率 24
-s 1920X1080 # 指定输出视频分辨率
-vb 5000k 将输出文件视频码率限制在 5000k左右
-ab 320k 将输出音频码率设置限制在320k左右


# 将视频音频拆分，输出生成多个文件
ffmpeg -i hello.avi -c:v h264 video.map -c:a aac audio.acc

# 将4k视频同时生成 不同分辨率视频输出
ffmpeg -i video4k.avi -c:v h264 -s 1280x720 720p.map -c:v h264 -s 1920x1080 1080p.mp4

# 主要参数
-i 设定输入流 
-f 设定输出格式 
-ss 开始时间 
# 视频参数：
-b 设定视频流量(码率)，默认为200Kbit/s 
-r 设定帧速率，默认为25 
-s 设定画面的宽与高 
-aspect 设定画面的比例 
-vn 不处理视频 
-vcodec 设定视频编解码器，未设定时则使用与输入流相同的编解码器
# 音频参数：
-ar 设定采样率 
-ac 设定声音的Channel数 
-acodec 设定声音编解码器，未设定时则使用与输入流相同的编解码器 
-an 不处理音频
```

> [基础使用参考链接](https://www.jianshu.com/p/ddafe46827b7)
>
> [简单示例](https://zhuanlan.zhihu.com/p/67878761)

### ffplay

> 继承了 ffmpeg 的编解码能力，支持格式最全的播放器

```shell
# 播放文件，左右方向键控制快进快退，q退出
ffplay video.mp4
```



在安装 ffmpeg 时 本来时默认会 安装好 ffplay,ffprobe。 但是如果缺少依赖会导致安装不成功

ffplay 依赖 [SDL2](https://github.com/libsdl-org/SDL/releases/tag/release-2.26.4)

```shell
// 设置 ffmpeg   --enable-libx264，此设置依赖x264。不然报错 libx264 not found
./configure --enable-shared --enable-libx264 --enable-gpl --prefix=/usr/local/ffmpeg

make && make install
```

**5. 安装x264**

```shell
http://www.videolan.org/developers/x264.html，下载
或者

git clone https://code.videolan.org/videolan/x264.git

或者

git clone http://git.videolan.org/git/x264.git

cd x264

./configure --enable-shared

make & make install
```

**libx264 not found**

> 1. 将使用到的库路径添加至环境变量中
>
> export PATH=/usr/local/x264/bin:$PATH
>
> export PATH=/usr/local/x264/include:$PATH
>
> export PATH=/usr/local/x264/lib:$PATH

**手动编译安装ffmpeg会出现的问题**

> 在跑视频流程序或命令时出现这个问题。
>  跟这个报错：ffmpeg: error while loading shared libraries: libx264.so.164: cannot open shared object file: No such file or directory
>
>  ffmpeg 时使用到了 libx264，而在实际的编译式安装过程中没有指定编译 libx264 参数，从而产生报错。

**缺失依赖**

ffmpeg 命令报错 xxxx => not found

```shell
# ffmpeg bin名录下
ldd ffmpeg
```

查找目录

```shell
find /usr -name 'libavdevice.so.58'
```

将 目录export 出来

```shell
export LD_LIBRARY_PATH=/usr/local/lib/
```



### AKStream

> ​	AKStream是一套全功能的软NVR接口平台，软NVR指的是软件定义的NVR（Network Video Recoder）。
>
> ​	AKStream集成了ZLMediaKit作为其流媒体服务器，AKStream支持对ZLMediaKit的集群管理（通过AKStreamKeeper-流媒体治理组件），可以将分布在不同服务器的多个ZLMediaKit集群起来，统一管理，统一调度。

**AKStream生态圈开源Web管理平台**

- 基于React的纯前端AKStream Web UI
- 非常简单的部署方式，非常简单的运行方式
- https://gitee.com/sscboshi/AKStreamNVR
- https://github.com/langmansh/AKStreamNVR

- 基于.net 5和vue2
- https://github.com/langmansh/AKStreamUI
- https://gitee.com/sscboshi/AKStreamUI





### Nginx

> 安装 Nginx,nginx-http-flv-module插件

配置文件

```
...
 
http {
    include       mime.types;
    default_type  application/octet-stream;
 
    keepalive_timeout  65;
 
    server {
        listen       80; #http-flv的拉流端口
 
        ...
        
        # http-flv的相关配置
        location /test {
            flv_live on; #打开HTTP播放FLV直播流功能
            chunked_transfer_encoding on; #支持'Transfer-Encoding: chunked'方式回复
 
            add_header 'Access-Control-Allow-Origin' '*'; #添加额外的HTTP头
            add_header 'Access-Control-Allow-Credentials' 'true'; #添加额外的HTTP头
        }
 
        ...
    }
}
 
rtmp_auto_push on;
rtmp_auto_push_reconnect 1s;
rtmp_socket_dir /tmp;
 
rtmp {
    out_queue           4096;
    out_cork            8;
    max_streams         128;
    timeout             15s;
    drop_idle_publisher 15s;
 
    log_interval 5s; #log模块在access.log中记录日志的间隔时间，对调试非常有用
    log_size     1m; #log模块用来记录日志的缓冲区大小
 
    server {
        listen 1935;
        chunk_size: 4096;
        # server_name www.test.*; #用于虚拟主机名后缀通配
 		
        #ffmpeg推流的application 
        application live {
            live on; # 启用实时流应用程序监听
            allow publish 127.0.0.1;
            allow play all;
            record off; # 不要录制流
            meta copy; # 复制传出元数据的传入元数据
            gop_cache on; #打开GOP缓存，减少首屏等待时间 on时第一帧加载快，off时第一帧加载慢 
            # @StringKai 在博客https://blog.csdn.net/string_kai/article/details/100598268提到on时延高，off时延低，不过我在测试时并没有感觉出时延的差别
        }
 
       ...
    }
 
   ...
}
```

使用 ffmpeg 推流

```
ffmpeg -f v4l2 -framerate 10 -i /dev/video0 -g 10 -f flv rtmp://127.0.0.1/live/wei
```

播放：

RTMP协议拉流:

```
rtmp:192.168.94.128/live/wei
```

HTTL FLV 协议拉流

```
http://192.168.94.128/test?app=live&stream=wei
```

![image-20230409153038364](https://raw.githubusercontent.com/wyf195075595/images/main/blog/image-20230409153038364.png)

