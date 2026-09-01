---
title: gitlab安装使用
date: 2022-05-01 23:27:46
tags:
     - gitea
---


### 2026年8月更新-gitea
gitlab占用内存和消耗过高，可替换成低功耗的**gitea**

[关于gitea](https://docs.gitea.com/zh-cn/)

宝塔docker里面包含了gitea应用直接安装使用即可


### 开始
想在自己的服务器上搭建一个gitlab,保存代码啥的，发现gitlab要求最低配置2核4g内存，自己的服务器够呛。
[gitlab安装参考](https://help.aliyun.com/document_detail/52857.html)

### 手动部署
1. 安装依赖包

```
sudo yum install -y curl policycoreutils-python openssh-server
```

2. 依次运行一下命令，配置SSH服务

```
sudo systemctl start sshd //启动SSH服务
sudo systemctl enable sshd //设置SSH服务为开机自启动
```

3. 安装Postfix来发送通知邮件

```
sudo yum install postfix
```

4. 设置Postfix开机启动

```
sudo systemctl enable postfix
```

5. 启动Postfix服务
```
vim /etc/postfix/main.cf//打开文件
inet_interfaces = all//修改文件中代码
保存关闭文件
sudo systemctl start postfix//启动Postfix服务
```

6. 添加GitLab软件包仓库
```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | sudo bash
```

7. 安装GitLab
```
sudo EXTERNAL_URL="GitLab服务器的公网IP地址:端口" yum install -y gitlab-ce
```

8. 打开网页
用户名root
密码在/etc/gitlab/initial_root_password文件中

### gitlab内存占用过大问题修改

[gitlab内存占用过大问题参考](https://ouyangpeng.blog.csdn.net/article/details/84066417)

编辑/etc/gitlab/gitlab.rb文件
添加
unicorn['worker_timeout'] = 20
unicorn['worker_processes'] = 2
postgresql['shared_buffers'] = "128MB"
postgresql['max_worker_processes'] = 4
sidekiq['concurrency'] = 5

运行gitlab-ctl reconfigure命令
gitlab-ctl restart
### 使用
自己的2核4g服务器无法使用，只能打开个网页，一直转圈卡住。