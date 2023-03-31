---
title: h5播放rtsp格式视频
date: 2023-03-27 10:54:10
tags: rtsp
categories: linux
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

<!--more-->

### zlmediakit搭建和配合ffmpeg使用

> [zlmediakit](https://github.com/ZLMediaKit/ZLMediaKit) 是一个基于C++11的高性能运营级流媒体服务框架，可以推送各种视频，音频流

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

- [附带推流测试地址(附带右边列表很多相关资料)：](https://github.com/ZLMediaKit/ZLMediaKit/wiki/ZLMediaKit%E6%8E%A8%E6%B5%81%E6%B5%8B%E8%AF%95)

	

> [参考地址](https://blog.csdn.net/weixin_43248279/article/details/124734505)

[ZIMediaKit源地h1 (https://qiteecom/xia-chu/ZIMediaKit

```js
// rtsp推流(文件推流)
ffmpeg -re -i test.mp4 -rtsp transport tcp -c copy -f rtsp rtsp://127.0.0.1:554/live/test
// rtsp推流(文件循环推流)
ffmpeg-re -stream_loop -1 -i test.mp4 -rtsp_transport tcp -c copy -f rtsp rtsp://127.0.0.1:554/live/test
// rtmp推流(文件推流)
Ffmpeg -re -i test.mp4 -vcodec h264_nvenc -acodec aac -f flv rtmp://127.0.0.1:1935/live/test
// rtmp推流(文件循环推流)
ffmpeg -re -stream_loop -1 -i test.mp4 -vcodec h264 -acodec aac -f flv rtmp://127.0.0.1:1935/live/test

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
```



### ffplay

> 继承了 ffmpeg 的编解码能力，支持格式最全的播放器

```shell
# 播放文件，左右方向键控制快进快退，q退出
ffplay video.mp4

```

