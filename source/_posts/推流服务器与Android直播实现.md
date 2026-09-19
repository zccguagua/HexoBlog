---
title: 推流服务器与Android直播实现
date: 2020-01-06 23:10:31
tags:
  - Android
categories:
  - Android
---
![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_04.jpg "Image")

### 前言
安卓端实现视频直播/观看，尝试自己搭建一个推流服务器，为了简单方便采用SRS(Simple RTMP Server)开源流媒体服务器

系统我使用的是centOS6.5，下载安装虚拟机和centOS可参考其他文章

SRS文档：[SRS](https://github.com/ossrs/srs/wiki/v1_CN_Build)

### 安装使用
首先clone srs源码
```
git clone https://github.com/ossrs/srs
cd srs/trunk
```

编译和启动

```
./configure && make
指定配置文件，即可启动SRS：
./objs/srs -c conf/srs.conf
```

至此，推流服务器已经搭建完成，超级简单啊

### 测试推流拉流

可用obs采集摄像头推流 [OBS下载](https://obsproject.com/)

使用方法可参考这篇博客：[开源流媒体服务器SRS学习笔记](https://www.cnblogs.com/yjmyzz/p/srs_study_1_install_push_and_pull_stream.html )

不同端拉流可以用VLC开源播放器 [VLC官网](https://www.videolan.org/)

输入推流地址即可查看到推流视频 "rtmp://your_server_ip:1935/live/livestream"

### Android端截屏推流
安卓端屏幕推流可参考这篇博客：[Android实现录屏直播 ](https://blog.csdn.net/zxccxzzxz/article/details/55230272 )

GitHub地址：[传送门](https://github.com/eterrao/ScreenRecorder )

效果图:

![rtmp](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/rtmp.jpg )

PS:局域网内使用会有卡顿和延迟
