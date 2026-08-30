---
title: DeepFaceLab AI换脸
date: 2020-04-12 23:26:02
tags:
        - DeepFaceLab
---
![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/deepfacelab/deepfacelab_01.jpg "Image")

### 开始
看到网上一个换脸宋小宝的视频觉得挺有意思而且换脸的效果相当真实，很早之前就耳闻换脸了，特地来研究一下

### DeepFaceLab 
换脸的软件有好几种，听介绍说DeepFaceLab这个软件使用简单且效果不错，就来学习一下这个软件的使用

[DeepFaceLab传送门](https://github.com/iperov/DeepFaceLab)

#### 使用
软件教程[DeepFaceLab中文网](https://www.deepfaker.xyz/)这个网站有介绍

软件解压后安装下图的顺序依次点击就可以
![DeepFaceLab](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/deepfacelab/deepfacelab_02.jpg "use")

第6步是训练操作，是最耗时的操作，这一步会跳出预览窗口可以看到图中的黄蓝线（迭代曲线）、迭代数（Iter）。

![第6步](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/deepfacelab/deepfacelab_03.jpg "use")

这一步可以根据动态参数的最后两列Loss值（值越小越好，建议低于0.2）、黄色线所占的比例、最后一列的人脸清晰度来停止训练。
这一步可以保存，再次打开会继续上次的参数训练
![第7步](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/deepfacelab/deepfacelab_04.png "use")

第7步是合并操作，根据弹出的操作提示可以操作调整合成的效果 
按x切换遮罩模式，
按WS调整人脸的遮罩范围，
按ED调整脸的边缘羽化，
按TG调整人脸的清晰度分辨率，
按/讲当前一张调整的参数覆盖到下一张的参数

合成效果
 {% dplayer "url=https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/deepfacelab/deepfacelab_meidui.mp4" 'autoplay=false'  %}



