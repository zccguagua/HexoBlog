---
title: ChatGPT使用教程
date: 2023-03-15 22:25:46
tags:
    - ChatGPT
categories:
  - 知识
---
![ChatGPT](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/chatgpt/chatgpt_logo.png)
### 准备
1.需要科学上网
2.ios部分app需要使用国外AppleId才能安装
### 注册账号
注册ChatGPT账号,国内手机无法注册,当时使用一个虚拟手机号注册的,使用谷歌账号登录,之后就能用谷歌账号直接登录了,
[1块钱注册火爆全网的ChatGPT机器人](https://www.bilibili.com/video/BV1GW4y1g7sV/?spm_id_from=333.337.search-card.all.click&vd_source=34740428362c1e9dfe3a72a6b2673264)

### 客户端
[ChatGPT 客户端 (Mac, Windows and Linux)](https://github.com/lencx/ChatGPT)

5月19日OpenAI官方ChatGPT app上架appstore


### 如何充值ChatGPT
5月19日OpenAI官方ChatGPT app上架appstore 可以直接在手机上订阅

<font color=red>~~注意:此方法本人测试绑定卡号未成功，此处只作为记录~~</font>
~~参考教程,这个教程详细的写了充值步骤~~
[~~ChatGPT Plus官方推荐新手教程~~](https://chatgpt-plus.github.io/chatgpt-plus/)

[~~手把手教你ChatGPT API Key申请使用和充值方法~~](https://cloud.tencent.com/developer/article/2232954)

[~~欧易~~](https://www.okx.com/cn)
~~ps:欧易购买USDT后需要24小时才能提现，~~<font color=#FFA500>~~注意：开卡10$,手续费1$,最低交易额10$~~</font>~~也就是最少准备22刀，还有这个卡月费2刀，有点贵。~~
<font color=#FFA500>~~注意：有的时候无法使用TRC20提币，需要谨慎~~</font>
### 免费ChatGPT
[这儿收集了一些免费好用的ChatGPT镜像站点](https://github.com/xx025/carrot)

[免费openai Key](https://freeopenai.xyz/)

[Poe](https://poe.com/)


### 在微信上迅速接入 ChatGPT，让它成为你最好的助手！

首先确保你的微信是否能登录微信网页版
[微信网页版](https://wx.qq.com/)

项目地址:
[wechat-chatgpt(github)](https://github.com/fuergaosi233/wechat-chatgpt)
参考教程中方法,我使用Railway进行部署
[Railway](https://railway.app/new)
<font color=#FFA500>注意:Railway内存只有500m,如果出现page crashed报错,很可能是内存问题。升级到开发者模式有8g内存</font>
[项目Issues](https://github.com/fuergaosi233/wechat-chatgpt/issues/770)
docker部署方法未成功,待测

<font color=red>注意:此方法有可能会被腾讯检测到外挂插件</font>
我的微信号被提醒了一次，不敢用了
### 搭建自己的ChatGPT网站
[参考：chatgpt-web](https://github.com/Chanzhaoyu/chatgpt-web)
部署到Railway后,点击项目-> setting -> Domains -> 可以创建一个访问地址，
可以绑定自己的域名
用免费的openai key，既可拥有自己的ChatGPT啦,

### 微信公众号集成ChatGPT
ps:railway微信公众号无法访问,我是搭在我的天翼云ubuntu上，需要开启clash代理，否则无法访问openai
[参考项目chatgpt-on-wechat](https://github.com/zhayujie/chatgpt-on-wechat)
[使用文档](https://github.com/zhayujie/chatgpt-on-wechat/blob/master/channel/wechatmp/README.md)
ps:运行程序的时候一开始会提示缺少库，一个一个安装就行
我的设置iptables端口转发到80时，没有加sudo，运行app.py时也没有加sudo
##### 代理：
[Clash for linux 教程](https://w1.v2free.top/doc/#/linux/clash)
ps:我的订阅下载的是字符串文件，不是yaml规则文件，我是把windows电脑上clash的yml配置文件替换到~/.config/clash/config.yaml中去的
```
//其他命令
nohup ./clash & tail -f nohup.out//后台运行
jobs -l //查看后台进程
kill %编号 //杀死进程
touch 文件.后缀 //新建文件
nohup python3 app.py & tail -f nohup.out //后台运行并打印日志到nohub.out
ps -ef | grep clash //查看进程编号
lsof -i 8080 //显示8080端口进程
```

### 无阻访问OpenAI API
不用配置代理啦
[通过Cloudflare Worker无阻访问OpenAI API](https://mp.weixin.qq.com/s/SYGPuHKO7ShzfmBGsQ363w)
[eu.org](https://nic.eu.org/arf/en/)
[cloudflare](https://dash.cloudflare.com/)
[ChatGPT-Next-Web](https://github.com/Yidadaa/ChatGPT-Next-Web)
[vercel](https://vercel.com/)

注意：cloudflare设置严格格式
![cloudflare](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/chatgpt/cloudflare_ssl.png)

### 企业微信接入ChatGPT
[一键为你的企业微信接入ChatGPT](https://mp.weixin.qq.com/s/Ultc3LKDnoX9VRW-S9tXhw)
railway注意端口填80，json编辑配置是无法使字符[]
[插件](https://github.com/goldfishh/chatgpt-on-wechat/blob/master/plugins/tool/README.md)
[每日60秒早报](https://alapi.cn/api/view/93)

### 企业微信部署ChatGPT
[企业微信部署ChatGPT](https://www.wangpc.cc/aigc/wechat_com-chatgpt/)


### ps:如何注册国外AppleId
网上教程很多随便找一个
[手把手教你注册苹果账号（创建Apple ID）](https://www.bilibili.com/video/BV1yE411571S/?spm_id_from=333.337.search-card.all.click&vd_source=34740428362c1e9dfe3a72a6b2673264)

### new Bing搜索
据说谷歌搜索市场占比93%，现在微软向谷歌发起挑战，压力来到了谷歌这边
[集合ChatGPT的Bing搜索](https://www.bilibili.com/video/BV1ev4y1s7pb/?spm_id_from=333.880.my_history.page.click&vd_source=34740428362c1e9dfe3a72a6b2673264)

### Midjourney 使用教程
[midjourney 教程](https://www.bilibili.com/video/BV1kG4y1F7kY/?spm_id_from=333.880.my_history.page.click&vd_source=34740428362c1e9dfe3a72a6b2673264)

