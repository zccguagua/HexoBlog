---
title: Hexo博客创建与使用
date: 2018-05-21 23:24:14
tags: 
  - Hexo
  - 服务器
categories:
  - 服务器
---


![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/food.jpg "Image")

### 博客创建

博客安装配置参考下方链接

[hexo史上最全搭建教程](https://blog.csdn.net/sinat_37781304/article/details/82729029)
[Hexo博客添加SEO-评论系统-阅读统计-站长统计 ](http://visugar.com/2017/08/01/20170801HexoPlugins/)

### 博客使用
本博客使用 [Hexo](https://hexo.io/zh-cn/) 主题,所以部分方法适用于Hexo为主题的博客

在博客目录/myblog鼠标右键 Git Bash Here
```
    //注意不要用淘宝镜像会出现问题，使用：npm set registry https://registry.npmjs.org/
    hexo clean #清除缓存 网页正常情况下可以忽略此条命令
    hexo generate #生成
    hexo server #启动服务预览，非必要，可本地浏览网页
    //打开http://localhost:4000/，发现刚才的文章已经成功了
    
    hexo deploy #部署发布

    hexo new "博客题目" #新建文章
```
  添加图片
```
    {% img [food] /images/food.png 食物 %}
    ![Image](https://your-link/rtmp.jpg "Image")
```
   
 绑定域名
 
```
    新建CNAME文件 里面写上你的域名
```

超链接
```

[超链接名](超链接地址 "超链接title")
[网站名称](http://zcccool.cn)

```
在文章中添加分类与标签
```
--- title: 文章标题
 date: 2018-10-22 01:16:24 
 categories: "云服务器" (#分类) 
 tags: (#标签) 
       - centOS 
       - tomcat
---

```
增加锚点
```
1. 使用Markdown的语法来增加跳转链接：“[名称](#id)”。
2.<span id = "jump">跳转到的位置</span>
```

[GitHub 连接失败解决方案](https://blog.csdn.net/lph159/article/details/143783520)


### 添加背景音乐

在themes/hueman/_config.yml
```
widgets:
    - catalog
    - recent_posts
    - category
    - archive
    - tag
    - tagcloud
    - links
    - myleft #此处添加了自定义的侧边栏

```
在themes/hueman/layout/widget/myleft.ejs里添加音乐ifragment
```
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=300 height=86
            src="//music.163.com/outchain/player?type=2&id=28285910&auto=1&height=66"></iframe>
```
### 添加导航栏
1.在G:themes\hueman\languages\zh-CN.yml中添加英文对照

```
index:
    home: '主页'
    about: '关于'
    test: '测试导航栏'  #添加的导航栏
    search: '搜索'
    archive: '归档'
    category: '分类：'
    uncategorized: '未分类'
    tag: '标签：'
```
2.在themes/hueman/_config.yml中
```
menu:
    Home: /
    # Delete this row if you don't want categories in your header nav bar
    Categories:
    About: /about/index.html
    Test: /test/test.html  #添加的导航栏html文件
```
3.在sourse文件夹下新建文件夹test再新建test.html即可添加导航页面


### 文章中插入音频和视频
1.在cmd 安装两个插件
```
    hexo-tag-aplayer
    hexo-tag-dplayer
```
2.在你本地安装博客的根目录下右击鼠标打开Git Bash ，运行如下两条命令来分别安装以上的两款插件：
```
npm install hexo-tag-dplayer
npm install hexo-tag-aplayer 
```
3.使用
```
{% aplayer "歌名" "歌手" "url" "autoplay=false" %}
{% dplayer key=value ... %}
```
4.更新主题
```
npm outdated #检查插件更新
更改package.json里面的版本号
npm install --save #开始更新
hexo version #查看版本号
```
### 文章访问计数

[不蒜子](http://busuanzi.ibruce.info/)

[不蒜子添加访问统计]{https://www.shuzhiduo.com/A/1O5E2lrGd7/}
修改文章访问次数在article.ejs

### 阿里云域名绑定github博客
1.ping 一下自己的博客地址 获得ip：
```
ping zccguagua.github.io 
```
2.打开阿里云的域名解析，填入刚刚的ip进行解析
3.在自己的hexo项目source文件夹下添加CNAME文件，在文件填入自己的域名"zcccool.cn"
4.在github上修改custom domin 设置自己的域名zcccool.cn

### 百度收录
[百度收录](https://cloud.tencent.com/developer/article/1858762)

### 看板娘
[看板娘](https://github.com/stevenjoezhang/live2d-widget)
[hexo安装音乐、追番页、live2d看板娘安装](https://www.bilibili.com/video/BV1rS4y1W78M/)
### 其他

  themes/hueman/layout/common/head.ejs  
  themes/hueman/layout/common/footer.ejs  
  对应页面的头部，底部，可添加自己的通用js,css等