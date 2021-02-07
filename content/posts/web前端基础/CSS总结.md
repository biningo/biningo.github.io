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

```css
a.f{color: red;} /*仅仅对a标签下的class=f有效*/
.f1,.f2,.f3{ color: blue;} /*组合选择*/
```

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

```css
div a{color: red;} /*只要是子元素都会匹配*/
div > a{color: green;} /*只匹配一层子元素*/
div + a{color: hotpink;} /*只匹配直接相邻的 中间不能有元素*/
div ~ a {color:red} /*匹配相邻元素 只要在同一个层级即可*/
```

```css
a:hover{font-size: 30px;} /*移动上去之后的*/
```

​    



## 参考

- https://juejin.cn/post/6844904137742614542



