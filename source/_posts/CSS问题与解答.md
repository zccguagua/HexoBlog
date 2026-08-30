---
title: CSS问题与解答
date: 2018-07-19 23:25:11
tags: 
        - CSS
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/b.jpg "Image")

[1.flex布局](#question_01)
[2.文字的下划线](#question_02)
[3.字符间距](#question_03)
[4.背景与图片](#question_04)
[5.完全居中](#question_05)
[6.消失隐藏](#question_06)
[7.div滚动条及插件](#question_07)
[8.换行](#question_08)
[9.隔行颜色](#question_09)
[10.媒体查询](#question_10)
[11.input 提示文字颜色](#question_11)
[12.表格文字超出隐藏](#question_12)
[13.css选择器](#question_13)
[14.各条边边框粗细](#question_14)
[15.动态计算长度值](#question_15)
[16.背景图片全屏且保持比例](#question_16)

### <span id="question_01">flex布局</span>
```
  .fdiv {
   /*父容器*/
    justify-content: flex-start; /*设置自容器沿主轴排列flex-end,center,space-around(均匀分布)，space-between(两端相切)*/
    align-items: flex-start; /*设置交叉轴(上下轴),flex-end,center,baseline(基线),stretch(拉伸)*/
    display: flex;
    flex-direction: row; /*设置排列方向 row-revere,column,column-reverse*/
        }
 .cdiv {
    /*子容器*/
    align-self: flex-start; /*字容器交叉轴。可覆盖父容器的align-items*/
       }
```

### <span id="question_02">文字的下划线</span>

```
 .dia1 {
     text-decoration: none; /*正常*/
     text-decoration: underline; /*下划线*/
     text-decoration: overline; /*上划线*/
     text-decoration: line-through; /*删除线*/
     cursor: pointer; /*wait,help,move,none(游标不可见)*/
      }
```

### <span id="question_03">字符间距</span>

```
 .div2 {
     letter-spacing: 20px; /*字符间距*/
     word-spacing: 20px; /*单词间距*/
     text-align: center; /*文字居中*/
       }
```
 ### <span id="question_04">背景与图片</span>

```
 img {
     vertical-align: middle; /*垂直对齐图片*/
     }

 .div4 {
     background-image: url("../images/img.png");
     background-repeat: no-repeat; /*背景图片不重复*/
     background-position: center bottom; /*背景图片位置，也可 10px 10px*/
     background-attachment: fixed; /*图片不随页面其余部分滚动*/
     background: rgba(98, 98, 98, 0.5); /*背景颜色不透明度*/
     background: url("../images/img.png") no-repeat 0 center; /*表示水平0，垂直居中*/
     background: url("../images/img.png") no-repeat -10px 20px; /*表示水平-10px，垂直20px*/
     background-image: linear-gradient(0deg, #ededed 0%, #ffffff 100%); /*背景色渐变*/
       }
```
      
 ### <span id="question_05">完全居中</span>

```
 .div5 {
   /*方法1*/
     position: absolute;
     margin: -60px 0 0 -60px /*自身高的一半，自身宽的一半*/
       }

 .div6 {
   /*方法2*/
     position: fixed;
     transform: translate(-50%, -50%);
        }
```
       
 ### <span id="question_06">消失隐藏</span>

```
 .div6 {
     display: none; /*不占地方的隐藏*/
     visibility: hidden; /*只隐藏，所占位置还在*/
       }
```
 ### <span id="question_07">div滚动条及插件</span>

```
 .div7 {
     overflow-x: auto; /*如果溢出则出现滚动条*/
     overflow-y: auto;
     height: 100px;
     width: 200px;
        }

```
[滚动条插件niceScroll](https://github.com/inuyaksa/jquery.nicescroll)
      
### <span id="question_08">换行</span>

```
 .div8 {
     word-break: break-all; /*允许在单词内换行*/
     word-break: keep-all; /*只能在半角空格或连字符处换行*/
        }

 禁止换行可用<nobr>
```

 ### <span id="question_09">隔行颜色</span>

```
 tbody tr:nth-child(2n+1) {
     background-color: #8ac16c;
        }

 tbody tr:nth-child(2n) {
     background-color: #afd9ee;
        }
```
### <span id="question_10">媒体查询</span>

```
 @media (max-width: 1280px) {
     /*小于1280px时的css*/
     /*注意：iframe时以上一层body参考*/
        }
```
 ### <span id="question_11">input 提示文字颜色</span>

```
 input::-webkit-input-placeholder {
     color: #afd9ee
        }

 input::-moz-placeholder {
     color: #afd9ee;
        }

 input:-ms-input-placeholder {
     color: #afd9ee;
        }
```
 ### <span id="question_12">表格文字超出隐藏</span>

```
 table {
     table-layout: fixed; /*定义fixed才起作用*/
       }

 td {
     word-break: keep-all; /*不换行*/
     white-space: nowrap;
     overflow: hidden; /*内容超出隱藏*/
     text-overflow: ellipsis; /*省略号*/
     /*可在td+title属性显示全部内容*/
        }
```
 ### <span id="question_13">css选择器</span>

```
 ul + p {
     /*ul的下一个p(紧贴的)相邻*/
        }

 input[type=checkbox]:checked + label:after {
 
        }

 ul > p {
     /*ul的直接子代(只一代)*/
        }

 ul ~ p {

     /*与ul+p相似，p的紧贴下所有p*/
        }

 ul:not(".div1") {
     /*所有的ul中不包含.div1的*/

        }

 li:not(:last-child) {
     /*除最后一个的其余所有li的样式*/
        }
```

### <span id="question_14">各条边边框粗细</span>

```
 .div9 {
     border-color: #afd9ee;
     border-style: solid;
     border-width: 1px 5px 5px 1px; /*设置边框粗细*/
        }
```
 ### <span id="question_15">动态计算长度值</span>

```
 .div10 {
    calc(100 % - 10 px);
        }
```

### <span id="question_16">背景图片全屏且保持比例</span>

```
.bg {
	background: url("images/login_bg.jpg") no-repeat center center;
	z-index: 0;
	background-size: cover;
	background-attachment: fixed;
	width: 100%;
	height: 100%;
	position: fixed;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
}
```