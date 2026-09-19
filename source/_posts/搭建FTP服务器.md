---
title: 搭建FTP服务器
date: 2021-11-10 00:27:44
tags:
  - 服务器
categories:
 - 服务器
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_20.jpg "ftp")


前段时间阿里云服务器到期了，趁着双11换了一个2核4g的天翼云服务器，记录一下安装FTP服务器过程
ps:天翼云服务器在没有备案的情况下，是使用不了80，8080，443等端口的，配置了安全组也不行，搞的当时不明所以浪费了很多时间

### 参考

安装ftp服务器可参考下方教程
 [阿里云ftp教程](https://help.aliyun.com/document_detail/60152.html?spm=5176.22414175.sslink.2.1ea4617cUgE4Zb)
 [天翼云ftp教程](https://www.ctyun.cn/h5/help2/10000015/10553754)

 ### 安装FTP服务器
 ```
yum install vsftpd -y
systemctl enable vsftpd.service    #设置FTP服务开机自启动
systemctl start vsftpd.service      #启动FTP服务
systemctl stop vsftpd.service      #停止FTP服务
netstat -antup | grep ftp       #查看FTP服务的端口号
```
 
```
useradd ftpadmin    #创建“ftpadmin”用户
passwd ftpadmin     #按照提示设置“ftpadmin”用户密码
mkdir /var/ftp/pub  #创建供FTP使用的文件目录
chown -R ftpadmin:ftpadmin /var/ftp/pub #更改文件目录所有者
```

修改配置文件,执行以下命令打开配置文件“vsftpd.conf”

```
vi /etc/vsftpd/vsftpd.conf
```

设置以下参数，不允许匿名登录FTP服务器，允许本地用户登录FTP服务器，并指定FTP本地用户使用的文件目录。

```
anonymous_enable=NO              #不允许匿名登录FTP服务器
local_enable=YES                 #允许本地用户登录FTP服务器
local_root=/var/ftp/work01       #FTP本地用户使用的文件目录
```

设置以下参数，限制用户只能访问自身的主目录。

```
chroot_local_user=YES                      #所有用户都被限制在其主目录
chroot_list_enable=YES                     #启用例外用户名单
chroot_list_file=/etc/vsftpd/chroot_list   #例外用户名单
allow_writeable_chroot=YES                
```

被动模式除了需要配置主动模式所需的所有参数外，还需要配置的参数如下：
设置以下参数，配置FTP支持被动模式。并指定FTP服务器的公网IP地址，以及可供访问的端口范围，端口范围请根据实际环境进行设置。

```
listen=YES                           
listen_ipv6=NO                       
pasv_address=xx.xx.xx.xx      #FTP服务器的公网IP地址
pasv_min_port=50000            #被动模式下的最小端口
pasv_max_port=50010            #被动模式下的最大端口
```
ps:ftp的默认端口应该是21，要去服务器控制台去配置安全组开放21及上方端口号
按Esc键退出编辑模式，并输入:wq保存后退出。
在“/etc/vsftpd/”目录下创建“chroot_list”文件。

```
touch chroot_list
```

“chroot_list”文件是限制在主目录下的例外用户名单。如果需要设置某个用户不受只可以访问其主目录的限制，可将对应的用户名写入该文件。

如果没有例外也必须要有“chroot_list”文件，内容可为空。
执行以下命令重启vsftpd服务使配置生效。

```
service vsftpd restart
```

测试
 
```
ftp localhost   #quit可退出
```

 如果-bash: ftp: command not found
 FTP命令没有安装
 
```
 yum install ftp
 ```

### 配置网络防火墙
```
systemctl start firewalld.service   #开启防火墙
systemctl enable firewalld.service  #防火墙开机启动
systemctl stop firewalld.service    #关闭防火墙
firewall-cmd --reload   #重启防火墙
firewall-cmd --state    #查看防火墙状态
firewall-cmd --list-ports   #查看开放的端口
firewall-cmd --permanent --zone=public --add-port=8080-8083/tcp #添加多个端口
firewall-cmd --permanent --zone=public --remove-port=81/tcp #删除某个端口
netstat -inpt   #查看各接口监听
netstat -an | grep 8989    #查看端口是否正常监听
```


### 使用FTP

在windows电脑文件路径栏输入ftp:ip

输入用户名密码就可以查看与上传文件了

ubuntu可以使用fileZilla连接,Android手机和iPhone都可以下载FTP应用app，这样可以多端共享了

ps:谷歌浏览器从90版本开始去除了FTP连接，试了火狐和edge都不能打开FTP了
ps:开启FTP服务器后控制台查看cpu使用率会飙升到100%，不知道为什么