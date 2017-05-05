---
title: CSS样式设置小技巧
date: 2016-12-04 19:55:49
categories: 前端知识
tags: HTML+CSS
description: CSS样式设置的一些小技巧，包括水平、垂直居中，隐性改变display类型，以及清除浮动的方法
---
# 一、水平居中设置
   行内元素居中设置：如果被设置元素为文本、图片等行内元素时，水平居中是通过给父元素设置 text-align:center 来实现的。代码示例如下：
```
HTML代码
<body>
  <div class="txtCenter">我想要在父容器中水平居中显示。</div>
</body>
CSS代码
<style>
  .txtCenter{
    text-align:center;
  }
</style>
```
   定宽块状元素居中设置：满足定宽和块状两个条件的元素是可以通过设置“左右margin”值为“auto”来实现居中的。代码示例如下：
```
HTML代码
<body>
  <div>我是定宽块状元素，哈哈，我要水平居中显示。</div>
</body>
CSS代码
<style>
div{
    border:1px solid red;/*为了显示居中效果明显为 div 设置了边框*/
    
    width:200px;/*定宽*/
    margin:20px auto;/* margin-left 与 margin-right 设置为 auto */
}
</style>
```
   不定宽块状元素居中设置：
       1. 加入 table 标签，是利用table标签的长度自适应性---即不定义其长度也不默认父元素body的长度（table其长度根据其内文本长度决定），因此可以看做一个定宽度块元素，然后再利用定宽度块状居中的margin的方法，使其水平居中。代码示例如下：
```
HTML代码
<div>
 <table>
  <tbody>
    <tr><td>
    <ul>
        <li>我是第一行文本</li>
        <li>我是第二行文本</li>
        <li>我是第三行文本</li>
    </ul>
    </td></tr>
  </tbody>
 </table>
</div>
CSS代码
<style>
table{
    border:1px solid;
    margin:0 auto;
}
</style>
```
        2. 设置 display: inline 方法：改变块级元素的 display 为 inline 类型（设置为 行内元素 显示），然后使用 text-align:center 来实现居中效果。代码如下：
```
HTML代码
<body>
<div class="container">
    <ul>
        <li><a href="#">1</a></li>
        <li><a href="#">2</a></li>
        <li><a href="#">3</a></li>
    </ul>
</div>
</body>
CSS代码
<style>
.container{
    text-align:center;
}
/* margin:0;padding:0（消除文本与div边框之间的间隙）*/
.container ul{
    list-style:none;
    margin:0;
    padding:0;
    display:inline;
}
/* margin-right:8px（设置li文本之间的间隔）*/
.container li{
    margin-right:8px;
    display:inline;
}
</style>
```
        3. 设置 position:relative 和 left:50%：通过给父元素设置 float，然后给父元素设置 position:relative 和 left:50%，子元素设置 position:relative 和 left: -50% 来实现水平居中。代码如下：
```
HTML代码
<body>
<div class="container">
    <ul>
        <li><a href="#">1</a></li>
        <li><a href="#">2</a></li>
        <li><a href="#">3</a></li>
    </ul>
</div>
</body>
CSS代码
<style>
.container{
    float:left;
    position:relative;
    left:50%
}

.container ul{
    list-style:none;
    margin:0;
    padding:0;
    
    position:relative;
    left:-50%;
}
.container li{float:left;display:inline;margin-right:8px;}
</style>
```
# 二、垂直居中设置
   父元素高度确定的单行文本:
       父元素高度确定的单行文本的竖直居中的方法是通过设置父元素的 height 和 line-height 高度一致来实现的。(height: 该元素的高度，line-height: 顾名思义，行高（行间距），指在文本中，行与行之间的 基线间的距离 )。代码如下：
```
HTML代码
<div class="container">
    hello, world!
</div>
CSS代码
<style>
.container{
    height:100px;
    line-height:100px;
    background:#999;
}
</style>
```
   父元素高度确定的多行文本:
       	1. 使用插入 table  (包括tbody、tr、td)标签，同时设置 vertical-align：middle。css 中有一个用于竖直居中的属性 vertical-align，在父元素设置此样式时，会对inline-block类型的子元素都有用。代码如下：
```
HTML代码
<body>
<table><tbody><tr><td class="wrap">
<div>
    <p>看我是否可以居中。</p>
</div>
</td></tr></tbody></table>
</body>
CSS代码
table td{height:500px;background:#ccc}
/*因为 td 标签默认情况下就默认设置了 vertical-align 为 middle，所以我们不需要显式地设置了。*/
```
		2. 在 chrome、firefox 及 IE8 以上的浏览器下可以设置块级元素的 display 为 table-cell（设置为表格单元显示），激活 vertical-align属性，但注意 IE6、7 并不支持这个样式, 兼容性比较差。代码如下:
```
HTML代码
<div class="container">
    <div>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
        <p>看我是否可以居中。</p>
    </div>
</div>
CSS代码
<style>
.container{
    height:300px;
    background:#ccc;
    display:table-cell;/*IE8以上及Chrome、Firefox*/
    vertical-align:middle;/*IE8以上及Chrome、Firefox*/
}
</style>
```
   这种方法的好处是不用添加多余的无意义的标签，但缺点也很明显，它的兼容性不是很好，不兼容 IE6、7而且这样修改display的block变成了table-cell，破坏了原有的块状元素的性质。
# 三、隐性改变display类型
   当为元素（不论之前是什么类型元素，display:none 除外）设置以下 2 个句之一：
		1. position : absolute 
		2. float : left 或 float:right 
   时，元素的display显示类型就会自动变为以 display:inline-block（块状元素）的方式显示，当然就可以设置元素的 width 和 height 了，且默认宽度不占满父元素。代码如下： 
```
HTML代码
<div class="container">
    <a href="#" title="">进入课程请单击这里</a>
</div>
CSS代码
<style>
.container a{
    position:absolute;
    width:200px;
    background:#ccc;
}
</style>
```
# 四、清除浮动的方法

	1. 改变css样式 clear: both;
	2. width:100%; overflow: hidden;