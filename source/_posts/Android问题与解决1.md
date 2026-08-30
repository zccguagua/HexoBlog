---
title: Android问题与解答1
date: 2018-07-08 01:50:34
tags:  
        - Android
        - .9图
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/two_pigs.jpg "Image")

[1.AndroidStudio减少文件的大小](#question_01)
[2.圆形图片](#question_02)
[3.圆形背景框](#question_03)
[4.导航栏](#question_04)
[5.反转List](#question_05)
[6.加分割横线](#question_06)
[7.填充满ImageView区域](#question_07)
[8.设置横竖屏](#question_08)
[9.快速编辑"我的界面"](#question_09)
[10.少量数据存储](#question_10)
[11.一些报错信息处理](#question_11)
[12.隐藏软键盘](#question_12)
[13.按钮点击波纹效果](#question_13)
[14.CardView使用方法，此时圆角不受内部元素影响](#question_14)
[15.改变指定字体颜色](#question_15)
[16.WebView的使用](#question_16)
[17.解决RecyclerView错乱](#question_17)
[18.前置位补0](#question_18)
[19..9图在线制作](#question_19)
[20.获取当前IP方法](#question_20)

### <span id="question_01">AndroidStudio减少文件的大小</span>

```
    手动删除以下文件或者目录
    Dir : ProjectFolder/build
    Dir : ProjectFolder/app/build
    Dir : ProjectFolder/.gradle
    Dir : ProjectFolder/.idea
    Dir : ProjectFolder/captures
    File : ProjectFolder/*.iml
    File : ProjectFolder/local.properties
    File : ProjectFolder/.DS_Store
    File : ProjectFolder/.externalNativeBuild
```


### <span id="question_02">圆形图片</span>

```
1.compile 'de.hdodenhof:circleimageview:2.1.0'
2.<de.hdodenhof.circleimageview.CircleImageView
        android:id="@+id/profile_image"
        android:layout_width="96dp"
        android:layout_height="96dp"
        android:src="@drawable/d"
        app:civ_border_color="#FFFFFF"
        app:civ_border_width="2dp"/>
```

### <span id="question_03">圆形背景框</span>

```
<? xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval"
    android:useLevel="false">
    <solid android:color="@color/accent_material_light"></solid>
    <size
        android:width="15dp"
        android:height="15dp"></size>
    <padding
        android:bottom="1dp"
        android:left="2dp"
        android:right="2dp"
        android:top="1dp"></padding>
</shape>
```
### <span id="question_04">导航栏</span>

```
BottomNavigationBar 使用add show来切换fragment不要使用replace
#隐藏软键盘
  InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);
             boolean isOpen = imm.isActive();//isOpen若返回true，则表示输入法打开
             if (isOpen) {
             //强制隐藏键盘
                 imm.hideSoftInputFromWindow(view.getWindowToken(), 0); 
             }
 InputMethodManager imm = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);  
 imm.showSoftInput(view,InputMethodManager.SHOW_FORCED); //表示强制显示
```

### <span id="question_05">反转List</span>

```
   Collections.reverse(messages);//翻转list
```

### <span id="question_06">加分割横线</span>
```
1.<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="@color/border_gray"/>
    <size android:height="1px"/>
</shape>

 2.   android:divider="@drawable/line_shape"
      android:showDividers="middle"
```

### <span id="question_07">填充满ImageView区域</span>

```
ImageView      android:scaleType="fitXY"
```
### <span id="question_08">设置横竖屏</span>

```
设置竖屏
 android:screenOrientation="portrait"
设置横屏
 android:screenOrientation="landscape"
```

### <span id="question_09">快速编辑"我的界面"</span>

```
compile 'com.leon:lsettingviewlibrary:1.7.0'
```
参考: [GitHub](https://github.com/leonHua/LSettingView)

### <span id="question_10">少量数据存储</span>

```
     SharedPreferences pref = this.getSharedPreferences("data", MODE_PRIVATE);
        SharedPreferences.Editor editor = pref.edit();
        editor.putString("url", url);
        editor.putString("map1", map.toString());
//        editor.putStringSet("map2", (Set<String>) map);
        editor.putString("path", path);
        editor.commit();
        SharedPreferences pref2 = this.getSharedPreferences("data", MODE_PRIVATE);
        String url2 = pref2.getString("url", null);//第二个参数为默认值
        String map12 = pref2.getString("map", null);
//        Set map22 = pref2.getStringSet("map2", null);
        String path2 = pref2.getString("path", null);
```

### <span id="question_11">一些报错信息处理</span>
```
This Gradle plugin requires a newer IDE able to request IDE model level 3 报错。

解决方法：
（1）： 升级android studio IDE的版本。
（2）：在项目的gradle.properties配置文件中加入以下这句：
      gradle.properties中：android.injected.build.model.only.versioned = 3
```
```
此问题表示依赖包冲突
 java.lang.NoSuchMethodError: No static method setOnApplyWindowInsetsListener
```

### <span id="question_12">隐藏软键盘</span>

```
思路一：
对最外层标签加事件
 InputMethodManager imm = (InputMethodManager)
 getSystemService(Context.INPUT_METHOD_SERVICE);
 imm.hideSoftInputFromWindow(v.getWindowToken(), 0);
```


### <span id="question_13">按钮点击波纹效果</span>

```
<ripple xmlns:android="http://schemas.android.com/apk/res/android"
        android:color="@color/green">
<!-- 按压的颜色 -->
<!-- 按钮未点击的样子 -->
<item android:drawable="@drawable/lb_grey_border4" />
</ripple>
```

### <span id="question_14">CardView使用方法，此时圆角不受内部元素影响</span>

```
<android.support.v7.widget.CardView
 android:layout_width="wrap_content"
 android:layout_height="wrap_content"
 android:layout_marginBottom="5dp"
 android:layout_marginLeft="40dp"
 android:layout_marginRight="40dp"
 app:cardCornerRadius="10dp"
 app:cardElevation="5dp">
```
### <span id="question_15">改变指定字体颜色</span>

```
  String source = " <font color='red'>提示：</font> 启动后分机待处理。";
  hintText.setText(Html.fromHtml(source));
```
### <span id="question_16">WebView的使用</span>
```
WebSettings webSettings = webView.getSettings();
//设置是否支持缩放
webSettings.setSupportZoom(true);
webSettings.setBuiltInZoomControls(true);
//设置是否显示缩放按钮
webSettings.setDisplayZoomControls(true);
//设置WebView是否允许执行JavaScript脚本，默认false，不允许
webSettings.setJavaScriptEnabled(true);
//设置自适应屏幕宽度
webSettings.setUseWideViewPort(true);
webSettings.setLoadWithOverviewMode(true);
```
 ### <span id="question_17">解决RecyclerView错乱</span>

 ```
   @Override
    public int getItemViewType(int position) {
        return position;
    }
 ```
### <span id="question_18">前置位补0</span>
```
 String.format("%06d", Integer.valueOf(bean[0])); //前置位补0
```

### <span id="question_19">.9图在线制作</span>
 [ .9图在线制作]( https://romannurik.github.io/AndroidAssetStudio/nine-patches.html#&sourceDensity=320&name=example)
 
### <span id="question_20">获取当前IP方法</span>

```
 	    public static String getHostIp() {
         try {
             Enumeration<NetworkInterface> allNetInterfaces = NetworkInterface.getNetworkInterfaces();
             while (allNetInterfaces.hasMoreElements()) {
                 NetworkInterface netInterface = (NetworkInterface) allNetInterfaces.nextElement();
                 Enumeration<InetAddress> addresses = netInterface.getInetAddresses();
                 while (addresses.hasMoreElements()) {
                     InetAddress ip = (InetAddress) addresses.nextElement();
                     if (ip != null
                             && ip instanceof Inet4Address
                             && !ip.isLoopbackAddress() //loopback地址即本机地址，IPv4的loopback范围是127.0.0.0 ~ 127.255.255.255
                             && ip.getHostAddress().indexOf(":") == -1) {
                         NbLogUtil.d("SystemInfoToC", "getHostIp:" + ip.getHostAddress());
                         return ip.getHostAddress();
                     }
                 }
             }
         } catch (Exception e) {
             e.printStackTrace();
         }
         return null;
     }
```


