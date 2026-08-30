---
title: Android手机刷机记录
date: 2020-02-28 00:59:57
tags:
        - 刷机
        - Android
---


![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_05.jpg "Image")

### 1.开始
突发奇想给尝试自己的老Android手机刷系统，记录一下刷机过程

#### 2.解锁Bootloader(BL解锁)
一开始上来什么也不懂就开始进入fastboot发现老是不成功
才发现手机是先要解锁的，目前华为已经取消官网解锁了，自己尝试了很多解锁方法始终不行
进入刷机机器人界面显示绿色"PHONE Locked"就是未解锁
Bootloader，通俗来讲就是锁住你手机的Recovery和Fastboot,来阻止第三方ROM对手机进行损失
以下是几条命令
```
fastboot devices ## 查看是否连接
fastboot flash recovery recovery.img ## 刷入recovery
fastboot flash system system.img
执行 adb reboot bootloader ## 进入 Bootloader 界面
fastboot flash boot .img
fastboot reboot ## 重启手机
```
未解锁就刷系统会提示"Command not allowed"

尝试多种方法无效后 还是找了淘宝解锁（据卖家说能解锁任何Android手机）。。。。。。

显示红字"PHONE UnLocked"标识解锁成功
![UnLocked](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/shuaji1.png "UnLocked")

### 3.刷机
解锁之后简单的方法使用"线刷宝"一键刷机即可,会提示刷机成功

![Success](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/shuji3.png "Success")


### 4.刷入第三方的 Recovery TWRP 
有TWRP方便后面安装刷入Magisk

Recovery 是安卓的恢复系统

TWRP 是一个著名的开源 Recovery 镜像

TWRP官网下载地址： [TWRP](https://twrp.me/ "TWRP")

我采用花粉俱乐部的方法，比较简单  [荣耀7 中文TWRP](https://club.huawei.com/thread-8620377-1-1-2520.html "TWRP")

![界面](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/shuji2.png "TWRP")


### 5.刷入Magisk 
方法参考     [线刷第三方Recovery+卡刷面具Magisk](https://www.52pojie.cn/thread-903485-1-3.html?proxy=1 "52pojie")

Magisk参考阅读：[少数派 - 每个 Android 玩家都不可错过的神器](https://sspai.com/post/53043 "TWRP") 

### 6.安装太极Tichi
在Magisk查找插件太极taichi

通过Magisk安装太极-阳模块（太极-阳比太极阴功能丰富）


### 7.畅玩微信
在太极里安装畅玩微信模块（中间需要重启微信）
这个模块功能丰富，可对微信做很多操作，运动步数，自动抢红包，修改骰子，自动回复（这个功能没试成功）等等等等，功能太多，其他功能仍在测试

刷机后真是发现新天地