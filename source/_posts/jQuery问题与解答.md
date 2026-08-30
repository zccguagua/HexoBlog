---
title: jQuery问题与解答
date: 2018-07-26 00:12:46
tags: 
        - jQuery
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/songshu.jpg "Image")

[1.选择筛选元素](#question_01)
[2.增加&移出元素](#question_02)
[3.页面无操作是定时](#question_03)
[4.按钮冻结指定时间](#question_04)
[5.setTime函数带多个参数](#question_05)
[6.传递select的值(赋值)](#question_06)
[7.手机电话号码验证](#question_07)
[8.通过自定义属性定位](#question_08)
[9.复选框](#question_09)
[10.data-id使用](#question_10)
[11.配置](#question_11)

### <span id="question_01">选择筛选元素</span>

```
    $(".div1").children().css("font-size", "40px");//所有子元素(第一级)
    $(".div2").children(":eq(1)").css("color", "red");//第二个子元素
    $(".div3").find("*").css("color", "red");//每个后代元素(包括子代的后代)
    $(".div4").find("li:last").css("color", "red");//所有中最后一个li
    $("li").parents().css("color", "red");//所有li的父元素
    $("span").parent();//元素的的直接父元素(只上一级)
    $("span").next();//紧跟的下一个元素
    $("span").prev();//紧跟的上一个兄弟元素
    $("span").siblings();//所有同辈元素(不包括自己)
```

 ### <span id="question_02">增加&移出元素</span>

 ```
    $("p").detach();//移出p元素，但不删除p的事件
    $("p").remove();//会删除事件
    $("p").append();//在元素内部的最后添加
    $("p").prepend();//在元素内部的开头添加
    $("p").after();//在元素外部的后面
    $("p").before();//在元素外部的前面

    $("button").bind("click", {sid: this.id}, function (event) {
        onefunction(event.data.sid);
        //綁定带参数的方法
    });

    $("button").prop("onclick", null).off("click");//卸载点击事件
    $("button").trigger("click");//触发点击事件
```


 ### <span id="question_03">页面无操作是定时</span>

 ```
    //方法一
    var renovate = setInterval("xxfunction();", 3000);
    $("body").click(function () {
        window.clearInterval(renovate);
        renovate = setInterval("xxfunction();", 3000);
    });
```

 ### <span id="question_04">按钮冻结指定时间</span>

 ```
    $("button").click(function () {
        $("button".attr("disabled", true));
        var de = setTimeout("disabledTime()", 1000);
    });

    function disabledTime() {
        $("button").attr("disabled", false);
    }
```

 ### <span id="question_05">setTime函数带多个参数</span>

 ```
    setTimeout(function () {
        xxfunction(id, name, sex)
    }, 3000);
```

 ### <span id="question_06">传递select的值(赋值)</span>

 ```
    var allOption = document.getElementById("editSex").options;
    for (var i = 0; i < allOption.length; i++) {
        if (allOption[i].id == theon.sex)//根据id来判断
        {
            allOption[i].selected = true;
        }
    }
```
 ### <span id="question_07">手机电话号码验证</span>

 ```
    function checkTel(tel) {
        var mobile = /^1[3|5|8]\d{9}$/;
        phone = /^0\d{2,3}-?\d{7,8}$/;
        return mobile.test(tel) || phone.test(tel);//test:检测一个字符串是否匹配某个模式
    }
```

 ### <span id="question_08">通过自定义属性定位</span>

 ```
    var ss = data.nodeId;
    var tt = $('ul li[data-nodeid="' + ss + '"]');
    console.log(tt);//包含所有的data-nodeid
```

 ### <span id="question_09">复选框</span>

 ```
    $(this).is(':checked') == true;//复选框点击确认
    $("#ch").prop({checked: true});//复选框赋值
```

### <span id="question_10">data-id使用</span>

 ```
<div id = "user" data-uid = "12345" data-uname = "愚人码头" > </div>
user . getAttribute ( 'site' ) ;
user . setAttribute ( 'site' , 'http://www.css88.com' ) ;
// 读取标签定义属性 data-[key] = [value]
$("#id").data("key");
// 设置标签定义属性
//注意：无需$("#id").data('data-chid');
$("#changeModalData").data('chid');
 $("#changeModalData").data('chid', ss);
$("#id").data("key", "value");
 ```
 ### <span id="question_11">配置</span>

[webstrom配置less](https://www.cnblogs.com/hewasdrunk/p/7271700.html)

[webstorm配置Autoprefixer](https://www.cnblogs.com/summerXll/p/6516627.html)