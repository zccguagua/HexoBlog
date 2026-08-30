---
title: 小皮面板搭建nextcloud网盘 
date: 2021-11-14 15:32:01
tags:
        - nextcloud

---
![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_19.jpg "nextcloud")

### 2023年6月补充更新
<font color="red">ps:需要注意php版本，不在维护的php版本可能无法部署nextcloud,建议使用最新php版本</font>
<font color="red">ps:2023年6月，小皮面板已长时间没有更新维护，使用的php版本官方已不再维护，我替换成了宝塔，配置方法类似</font>

### 安装小皮面板

小皮面板：轻巧,便捷,功能强大,是国内最早,最知名的的免费集成环境，类似与宝塔。功能强大见者欣喜若狂。

[小皮面板网址](https://www.xp.cn/)

注意：小皮面板提示请确保您的系统是纯静的,未安装任何环境中涉及到的（Apache\Nginx\php\MySQL\FTP） 于是我先把自己的服务器重置了

我的系统是centos7.8 选择对应安装脚本命令

```
yum install -y wget && wget -O install.sh https://download.xp.cn/install.sh && sh install.sh
```

稍等片刻 终端会由成功提示

```
=================安装完成==================

请用浏览器访问面板:
外网:http://ip:9080/授权码
内网:http://ip:9080/授权码
系统初始账号:admin 系统初始密码:mdTVCZu9V6 官网:https://www.xp.cn
如果使用的是云服务器，请至安全组开放9080端口 如果使用ftp，请开放21以及30000-30050端口

===========================================
```

如提示要在服务器安全组打开9080端口，ftp的21端口和30000-30050端口。在浏览器地址输入“http://ip:9080/授权码”即可打开控制台界面

### 小皮面板安装数据库
先在小皮面板软件管理里安装Apache(我用是是Apache，nginx没试过，应该是一样的)、php、MySQL等软件

点击添加数据库，自己定义数据库信息，记住账号密码，到时候需要。如数据库名:nextcloud,用户名：nextcloud,密码：123456，访问权限选择所有人

![安装数据库](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_1.png "安装数据库")

### 新建站点

点击网站--添加站点，站点域名填写：abc.cn:8888，abc.cn是自己都买的域名，8888是用于nextcloud的端口号 点击保存即可。

ps：天翼云的80端口使用需要先备案，没备案的话可使用其他端口，我使用的是8888
ps：这里用到的8888端口同样要在服务器安全组打开。

![新建站点](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_2.png "新建站点")

### 创建nextcloud

nextcloud:开源免费的私有云存储项目，它能帮你快速在个人电脑或服务器上架设一套专属的私有云文件同步网盘。 跨平台支持windows 、mac、Android、iOS、Linux等平台

[nextcloud官网](https://nextcloud.com/)

[nextcloud GitHub](https://github.com/nextcloud/server)

去nextcloud GitHub下载两个东西，一个是源码包我用的是版本22.2.1，下载后文件名server-22.2.1.zip，另一个是这个3rdparty压缩包
![nextcloud](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_3.png "nextcloud")
![nextcloud](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_4.png "nextcloud")

### 部署

上传server-22.2.1.zip压缩包到你的站点目录，就是刚刚新建的/www/admin/abc.cn_8888/wwwroot
鼠标移到压缩包上解压，点击解压，解压成功后将service-22.2.1文件夹中的所有文件剪切到wwwroot的目录
![nextcloud](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_5.png "nextcloud")
注意这个3rdparty目录，默认是空的，需要将上一步下载的3rdparty包同样上传并解压到自己的这个3rdparty中。如果3rdparty是空的打开网页会提示

```
Composer autoloader not found, unable to continue.
Check the folder "3rdparty". 
Running "git submodule update --init" will initialize the git submodule that
handles the subfolder "3rdparty".
```

到此nextcloud部署结束 在浏览器输入:你的域名:8888,不出意外即可打开nextcloud界面
在界面上创建管理员账号，密码,配置数据库选择mysql，用户名、密码、数据库名都是刚刚设置的，端口号mysql默认是3306，点击安装完成，稍等片库即可大功告成

![nextcloud](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_6.png "nextcloud")

### 云盘界面

![nextcloud](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/nextcloud/nextcloud_7.png "nextcloud")

[自己配置网盘分享张图片](http://zccode.cn:10000/index.php/s/fZHiWCAtZCL9rpt)

ps:在appstore和谷歌商店下载各端的nextcloud，可以多端共享
