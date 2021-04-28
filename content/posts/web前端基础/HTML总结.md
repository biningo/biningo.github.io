---
title: HTML总结
date: 2019-05-25
categories: [web前端基础]
tags: [HTML,前端]
---

## head标签

head标签用于HTML文档头部，主要用于存放元数据、引入外部样式脚本等标签的容器，还可定义一些页面信息相关的内容，主要的子标签如下：

- `<meta>`：设置网页的元数据,比如网页的字符集.....

- `<link>`：引入外部资源，指定ref定义当前文档与被链接文档之间的关系

    指定type定义被链接的文档类型

- `<title>`：设置网页标题

- `<style>`：放置内嵌的样式表

- `<script>`：引入外部`js`脚本

```html
<head>
    <meta charset="UTF-8">
    <title>这是网页标题</title>
    <link rel="stylesheet" type="text/css" href="theme.css">
    <link rel="stylesheet" type="text/css" href="http://www.x.cn/a.css">
    <style>
        a{color: red;}
    </style>
    <script src="https://static.sick.cn/respond.min.js"></script>
    //下面脚本会执行
    <script>
        alert("hello,world")
    </script>
    //定义了这行则所有绝对地址的url则都会以这个为基础
    <base href="https://www.google.com">
</head>
```

​    

## meta元数据

meta标签用于设置一些元数据，并且可以定义一些浏览器的缓存策略，还可以定义网页关键字用于搜索引擎爬虫的爬取

```html
<!-- 定义网页文档的字符集 -->
<meta charset="utf-8" />
<!-- 网页作者 -->
<meta name="author" content="开源技术团队"/>
<!-- 网页地址 -->
<meta name="website" content="https://www.baidu.com"/>
<!-- 网页版权信息 -->
 <meta name="copyright" content="2020-2021 demo.com"/>
<!-- 网页关键字, 用于SEO -->
<meta name="keywords" content="meta,html"/>
<!-- 网页描述 -->
<meta name="description" content="网页描述"/>
<!-- 搜索引擎索引方式，一般为all，不用深究 -->
<meta name="robots" content="all" />
<!-- 移动端常用视口设置 -->
<meta name="viewport" content="width=device-width,initial-scale=1.0,maximum-scale=1.0, user-scalable=no"/>
<!-- 
  viewport参数详解：
  width：宽度（数值 / device-width）（默认为980 像素）
  height：高度（数值 / device-height）
  initial-scale：初始的缩放比例 （范围从>0 到10）
  minimum-scale：允许用户缩放到的最小比例
  maximum-scale：允许用户缩放到的最大比例
  user-scalable：用户是否可以手动缩 (no,yes)
 -->
```

meta还可以通过`http-equiv`用来覆盖 HTTP 回应的头信息字段，`content`属性是该字段的内容

```html
<!-- expires指定网页的过期时间。一旦网页过期，必须从服务器上下载。 -->
<meta http-equiv="expires" content="Fri, 12 Jan 2020 18:18:18 GMT"/>
<!-- 等待一定的时间刷新或跳转到其他url。下面1表示1秒 -->
<meta http-equiv="refresh" content="1; url=https://www.baidu.com"/>
<!-- 禁止浏览器从本地缓存中读取网页，即浏览器一旦离开网页在无法连接网络的情况下就无法访问到页面。 -->
<meta http-equiv="pragma" content="no-cache"/>
<!-- 也是设置cookie的一种方式，并且可以指定过期时间 -->
<meta http-equiv="set-cookie" content="name=value expires=Fri, 12 Jan 2001 18:18:18 GMT,path=/"/>
<!-- 使用浏览器版本 -->
<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
<!-- 针对WebApp全屏模式，隐藏状态栏/设置状态栏颜色，content的值为default | black | black-translucent -->
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent" />
```

​    

## 网页语义标签

```html
<header>
<footer>
<main>
<article>
<aside>
<section>
<nav>
<h1> ~ <h6>
<h1> ~ <h6>
<hgroup>
```

语义标签用于了解网页的大致结构，对页面布局没有太大影响

```html
<body>
  <header>页眉</header>
  <main>
    <article>
      <h1>文章标题</h1>
      <p>文章内容</p>
    </article>
  </main>
  <footer>页尾</footer>
</body>
```

​    

## 锚点

锚点用于定位一个页面中的位置，和书签一样，点击之后会在URL后面拼接`#xxx`，然后就会跳转到相应位置

```html
<a name="one">one</a>
<a name="two">two</a>
.....
<a href="#one">one</a>
<ah href="#two">two</a>
```

​    

## 文本修饰标签

```html
<strong></strong><b></b> 加粗
<samll></samll> 小号字体
<big></big> 大号字体
<sub></sub><sup></sup> 下标上标  比如 2^3
<em></em><i></i> 倾斜
<u></u>下划线
<p></p> 一段
<s></s> 删除线

<span></span><div></div> 一个行内一个块元素  无任何作用 只是一个容器
<br>
<hr>
```

​    

## 有序无序列表

有序列表`<ol>`

```html
<ol>
    <li>1</li>
    <li>2</li>
    <li>3</li>        
</ol>
```

无序列表`<ul>`

```html
<ul>
    <li>1</li>
    <li>1</li>
    <li>1</li>
</ul>
```

​    

## 表格

TODO

​        

## 表单

表单元素主要看`input`标签        

```html
<input autofocus  type="text" disable> #禁用输入、自动聚焦、文本类型
```

type的类型主要有如下：

| text                                              | 一个单行文本框，默认行为                       |
| ------------------------------------------------- | ---------------------------------------------- |
| password                                          | 隐藏字符的密码框                               |
| search                                            | 搜索框，在某些浏览器键入内容会出现叉标记取消   |
| submit、reset、button                             | 生成一个提交按钮、重置按钮、普通按钮           |
| number、range                                     | 只能输入数值的框；只能输入在一个数值范围的框   |
| checkbox、radio                                   | 复选框，用户勾选框；单选框，只能在几个中选一个 |
| image、color                                      | 生成一个图片按钮，颜色代码按钮                 |
| email、tel、url                                   | 生成一个检测电子邮件、号码、网址的文本框       |
| date、month、time、week、datetime、datetime-local | 获取日期和时间                                 |
| hidden                                            | 生成一个隐藏控件                               |
| file                                              | 生成一个上传控件                               |

```html
<label>姓名：</label><input type="text" autofocus /><br>
<label>性别：</label>男<input type="radio" name="sex" />&nbsp;女<input type="radio" name="sex" /><br>
<label>年龄：</label><input type="number" /><br>
<label>爱好：</label>
<input type="checkbox"/>打篮球
<input type="checkbox"/>踢足球
<input type="checkbox"/>乒乓球
<input type="checkbox"/>跑步
<br>
<label>出生日期：</label><input type="datetime"/><br>
<label>备注：</label><textarea></textarea><br>
<label>喜欢的颜色:</label><input type="color" /><br>
<label>邮箱:</label><input type="email" /><br>
<label>电话:</label><input type="tel" /><br>
<label>头像上传:</label><input type="file" /><br>
```

​    

## 图像音视频

`<img>`

```html
<img src="foo.jpg" />
图像超链接
<a href="example.html">
  <img src="foo.jpg">
</a>
```

`<video>`

```html
<!--controls表示显示进度条-->
<video src="example.mp4" controls>
  <p>你的浏览器不支持 HTML5 视频，请下载<a href="example.mp4">视频文件</a></p>
</video>

<!--视频播放器的大小是 400 x 400，会自动播放和循环播放，并且静音，还带有封面图-->
<video controls width="400" height="400"
       autoplay loop muted
       src="jsj.mp4"
       poster="poster.png">
</video>

<!--提供多个视频格式 防止浏览器不支持视频格式-->
<video controls>
  <source src="example.mp4" type="video/mp4">
  <source src="example.webm" type="video/webm">
  <p>你的浏览器不支持 HTML5 视频，请下载<a href="example.mp4">视频文件</a></p>
</video>
```

`audio`和`video`差不多

​    

## 块级元素和行内元素

**块级元素：**

- 占据独立的区域，默认占据外部容器的`100%`宽度
- 高度、行高、外边距、内边距都可控制
- css属性`display:block`为块级元素

```html
<div>、<form>、<label>、<h`1|2|3...`>、<hr>、<legend>、<ol><ul><li>、<p>、<table>
```

**行内元素：**

- 行内元素默认与其他元素在同一行，不产生换行
- css属性`display: inline`为行内元素
- **行内元素是没有长宽高的，同时无法设置内外边距和border**

```html
<a>、<img>、<input>、<textarea>、<span>、select、small、strike、strong、sub sup、u、em、big、br、i
```

**行内块元素：**

- 同时拥有块属性和行内属性的一类元素
- css属性`display:inline-block`

​    

## 参考

- https://juejin.cn/post/6868946515906265096
- https://www.bookstack.cn/books/html-tutorial
- https://www.bookstack.cn/books/liyanhui-html-css    
- https://cloud.tencent.com/developer/article/1432841 【禁用代码调试、左键、右键】

