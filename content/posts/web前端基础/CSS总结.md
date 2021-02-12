---
title: HTML总结
date: 2019-05-25
categories: [web前端基础]
tags: [HTML,前端]
draft: true
---

## 样式选择器

| **选择器**     | **名称**       | **说明**                  | **CSS 版本** |
| :------------- | :------------- | :------------------------ | :----------- |
| `*`            | 通用选择器     | 选择所有元素              | 2            |
| `<type>`       | 元素选择器     | 选择指定类型的元素        | 1            |
| #`<id>`        | id 选择器      | 选择指定 id 属性的元素    | 1            |
| .`<class>`     | class 选择器   | 选择指定 class 属性的元素 | 1            |
| [attr]系列     | 属性选择器     | 选择指定 attr 属性的元素  | 2 ~ 3        |
| s1,s2,s3…      | 分组选择器     | 选择多个选择器的元素      | 1            |
| s1 s2          | 后代选择器     | 选择指定选择器的后代元素  | 1            |
| s1 > s2        | 子选择器       | 选择指定选择器的子元素    | 2            |
| s1 + s2        | 相邻兄弟选择器 | 选择指定选择器相邻的元素  | 2            |
| s1 ~ s2        | 普通兄弟选择器 | 选择指定选择器后面的元素  | 3            |
| ::first-line   | 伪元素选择器   | 选择块级元素文本的首行    | 1            |
| ::first-letter | 伪元素选择器   | 选择块级元素文本的首字母  | 1            |
| ::before       | 伪元素选择器   | 选择元素之前插入内容      | 2            |
| ::after        | 伪元素选择器   | 选择元素之后插入内容      | 2            |

标签、类、id选择器

```css
a.f{color: red;} /*仅仅对a标签下的class=f有效*/
.f1,.f2,.f3{ color: blue;} /*组合选择*/
```

属性选择器

```css
/*注意 下面只能有一个生效*/
[href]{color: chartreuse;} /*只对带有href属性的有效*/
[href='/abc']{color: coral;} /*只对href=/abc的有效 */
[href^="http"] { color: orange;} /*只对以http开头的有效*/
[href$="com"] {color：red;} /*只对以.com结尾的有效*/
[href*="baidu"] { color: orange;} /*包含即有效*/

[class~="edf"] { font-size: 50px;} /*有多个值时匹配其中一个*/
[lang|="en"] { color: red;} /*属性值具有多个值且使用“-”号连接符分割的其中一个值的属性选择器   <i lang="en-us">HTML5</i> */
```

子代、兄弟元素选择器

```css
div a{color: red;} /*只要是子元素都会匹配*/
div > a{color: green;} /*只匹配一层子元素*/
div + a{color: hotpink;} /*只匹配直接相邻的 中间不能有元素*/
div ~ a {color:red} /*匹配相邻元素 只要在同一个层级即可*/
```

伪选择器

```css
a:hover{font-size: 30px;} /*移动上去之后的*/
```

​    

## 文本样式

`font-xxx`

```css
font-style: italic /*斜体*/
font-family: 楷体,微软雅黑,宋体; /*多指定几个 如果浏览器不支持则继续使用后面的*/
font-weight: 100 /*100-900  normal~bloder*/
font: italic 800 16px 楷体 /*复合写法 style weight size family */
font:800 60px 楷体
font: 50px 楷体  /*必须保留 size family 其他都可以省略 并且循序不可变*/
```

`text-xxx`

```css    
text-decoration: underline; /*下划线*/
text-decoration: none; /*无任何线*/
text-align: center; /*文本水平对齐方式*/
line-height: 100px; /*设置行高 行高：文本默认高度+上下边距平均分*/
```

> 可以根据`line-height`来设置文本的垂直居中方式：
>
> `line-height`>盒子高度：文本偏下
>
> `line-height`<盒子高度：文本偏上
>
> `line-height`=盒子高度：文本垂直居中

​    

## 背景

```css
background-color:red /*背景颜色 默认是transparent透明的背景*/
background-image: url(https://www.xxx.asd/a.jpg) /*背景图片 背景图片会压住背景颜色 不会覆盖取消*/
background-repeat: repeat-x; /*图片平铺方案 默认是reapeat*/
background-repeat: no-repeat; /*图片不平铺*/
background-position: center bottom; /*背景图片布局 水平 垂直*/
background-position: center; /*正中心居中 只写一个 另外一个默认是center*/
background-position: 100px 50px; /*精准定位 只写一个则另外一个默认center*/
background-attachment: fixed; /*背景不随着网页内容而滚动 scroll则是会滚动*/
background: block url(xxx) no-repeat fixed center top /*复合写法*/

background: rgba(0,0,0,0.3) /*最后一个rgb设置透明度*/
background: rgba(0,0,0,.3) /*省略0.3的0*/
```



## display

`display`用于控制一个元素是否为块、行内、行内块元素等

```css
display: inline
display: block
display: inline-block
```

**块元素**：

- 独占一行
- 高、宽、外边距、内边距都可以控制
- 宽度默认是父容器宽度`100%`
- 是一个容器及盒子，里面可以放行内、块元素
- **文字类块元素** 如`p`等不能放其他块元素

**行内元素**：

- 与相邻元素在同一行内
- 高、宽无法设置，默认宽高就是内容的宽高
- 只能放文本或则其他行内元素

**行内块元素：**

- 与相邻元素在同一行内
- 宽高边距可以设置，默认是内容宽高

​        

## CSS盒子模型

![](https://raw.githubusercontent.com/biningo/cdn/master/img/2015-10-03-css-30.jpg)

### 1、border

border主要有三个样式：`style` `color` `width`

```css
border-style: solid;
border-color: red;
border-width: 2px;
```

也可以写作一行：

```css
border: 1px solid red;
```

border也可以设置单个方向的边框`border-top`

```css
border-top:2px solid red;
border-top-style: solid;
```

同时边框样式可以层叠，**先下大范围，后写小范围，小的覆盖大的**  下面的代码设置上边框为`红色5px`，其他都为`蓝色2px`

```css
border: 2px solid blue;
border-top:5px solid red ;
```

**注意：width不包括border-width** ，如果 `width:200px` `border-width:10px`那么整个盒子的 **width** 大小就是`210px`

### 2、padding

设置内容和边框的边距：

```css    
padding-top: 1px
padding-bottom: 2px
padding-left: 3px
padding-right: 4px
```

也可以简写：

```css
padding: 1px 4px 2px 3px; /* 上 右 下 左 */
padding: 1px 3px 2px; /* 上 左右 下 */
padding: 10px 50px; /* 上下 左右 */
padding: 10px; /* 上下左右 */
```

**注意：padding的距离也是不算入width等距离中的，width只是content内容宽度，如果盒子没有指定width则padding不会撑开盒子**   

### 3、margin

使用方法和`padding`一样，用来控制盒子与盒子之间的距离，下面展示一些常用的案例

**左右居中** 只需要左右设置为`auto`即可，必须为 **块级盒子**

```css
margin: auto;
margin: 100px auto; /*上下边距为100px  左右居中*/
margin-left:auto; margin-right:auto;
```

### 4、margin塌陷问题

如果一个父亲元素下有一个子元素，子元素想要`margin-top:20px`也就是相对于父亲元素偏移20px，但是这样就会造成 **塌陷问题**  父亲元素会和子元素永远粘在一起，一起`margin`移动

```css
.content{
    width: 500px;
    height: 100px;
    background-color: greenyellow;
    margin: auto;
    /* margin-top: 100px; */
    /*border: 1px solid transparent;*/
}
.txt{
    width: 50%;
    height: 50%;
    background-color: red;
    margin-top: 20px;
}
<div class="content">
        <div class="txt"></div>
</div>
```

为了解决上面`margin`合并问题，可以给父亲元素加上 **透明border**  这样子元素就会相对于父亲进行`margin`移动了，`border`可以是上下左右看你向往哪个方向移动

```css
border: 1px solid transparent;
```

###  5、清除内外边距

网页元素标签div、p等原生就带有一些border边距，可以手动将这些边距进行清除，下面代码基本是 **css的第一行代码，必须写**

```css
*{
    margin:0;
    padding: 0;
}
```

### 6、处理溢出

当设置了元素固定尺寸且内容过大时，就会出现溢出的问题，可以通过 `overflow` 系列样式来控制它

```css
/*自动、隐藏、滚动条、直接溢出不处理(默认方式)*/
overflow-x:
overflow-y:
overflow: auto|hidden|scrool|visible    
```

​    

## 浮动



​    

## 网页布局

布局的三大核心：用CSS摆放盒子

- 盒子模型（标准流）：块级元素依次上下排下，行内元素依次左右排列
- 浮动
- 定位

**块级元素左右居中**

```css
margin: auto; /*设置margin-left margin-right为auto即可*/
```

**行内块元素左右居中** 只需要给父亲加`text-align:content`即可

```css
text-aligin:center
```

**文字上下垂直居中** `line-height`和父亲容器高度一样即可

```css
line-height: 41px; /*垂直居中 和height一样*/
```

​        

## 参考

- https://juejin.cn/post/6844904137742614542



