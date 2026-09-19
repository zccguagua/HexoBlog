---
title: 阿里云Tomcat部署与配置
date: 2018-11-08 23:11:14
tags:
  - 服务器
categories:
  - 服务器
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/cat.jpg "Image")


### 1.下载
下载两个文件
apache-tomcat-8.5.72.tar.gz
jdk-8u311-linux-x64.tar.gz
使用工具复制到/usr/local/java文件夹
### 2.安装java
##### 解压 
```
tar -zxvf jdk-8u311-linux-x64.tar.gz
```
##### 配置Java环境变量 进入编辑 vim /etc/profile

在文件最后添加下面的内容
(i 编辑,Esc 退出,:wq 保存退出,:q!退出)
```
export JAVA_HOME=/usr/local/java/jdk1.8.0_311
export JRE_HOME=/usr/local/java/jdk1.8.0_311/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
```

添加完成后，使环境变量生效
```
source /etc/profile
```
##### 检测是否成功
```
java -version
```

### 3.安装tomcat
##### 解压
```
tar -zxvf apache-tomcat-8.5.72.tar.gz
```

进入tomcat路径/bin
```
 ./startup.sh //启动命令
 ./shutdown.sh //停止命令
 ```
(注意：失败请检查8080端口是否开放，去服务器控制台安全组开启)


### 4.配置直接通过地址访问
进入conf修改server.xml
 ```
 <Connector port="80" protocol="HTTP/1.1" //端口改为80(需开放80端口)
 <Engine name="Catalina" defaultHost="地址/域名"> //地址
 <Host name="地址/域名"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
<Context path="" docBase="项目名" debug="0" reloadable="true" /> //在Host之间加上
 ```

### 5.Tomcat 开机启动
centos:
关闭Tomcat 
编辑vim /etc/rc.d/rc.local
添加以下内容
 ```
export JAVA_HOME=/usr/local/java/jdk路径
export JRE_HOME=$JAVA_HOME/jre
sh /usr/local/java/tomcat路径/bin/startup.sh
 ```

```
ubuntu:
注册开机启动:  update-rc.d  –f  tomcat  defaults
撤销开机启动:  update-rc.d  –f  tomcat  remove
```
 
### 6.服务器更新
 ```
 yum -y update
 ```


（rc.local需要有执行权限 chmod +x rc.local）
重启机器即可启动Tomcat

Tomcat日志在logs目录，进入目录查看日志：
tail -f catalina.out  

### 查看tomcat是否返回判断是否配置成功

```
curl ip:端口号
```


### 配置ssl证书
证书可以去腾讯免费ssl证书申请下载，我下载了jks格式证书，这个证书包含一个jks文件，一个密码，

放到tomcat文件夹下我新建了一个文件夹jks，然后进入conf修改server.xml配置证书路径
```
<Connector port="443" protocol="HTTP/1.1"
               maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
               clientAuth="false"
                keystoreFile="/usr/local/java/apache-tomcat-8.5.72/jks/zccode.cn.jks"
                keystorePass="证书密码" />
```

Mysql安装级部署参考下面链接
[阿里云服务器配置Mysql](https://blog.csdn.net/sihai12345/article/details/73381151?tdsourcetag=s_pcqq_aiomsg)

 ### 挂载磁盘
[挂载磁盘](https://www.ctyun.cn/document/10000019/10033639)




