---
title: awk和sed工具
date: 2021-02-28
categories: [Linux]
tags: [Linux]
draft: true
---

## awk和sed区别

他们都是Linux下的 **流处理工具**

- `awk`核心是**格式化**
- `sed` 核心是 **正则**

## cut

cut可以提供简单的列切割

```bash
-d #定义分割符  默认是空格
-f #指定显示的列
-c #以字符为单位切割
-b #字节为单位切割
```

```bash
cat cut.txt| cut -c-4 #[,4]
cat cut.txt| cut -c4- #[4,]
cat cut.txt| cut -c31-4 #1开始取4个
cat cut.txt| cut -d ':' -f 1
```

​    

## awk

TODO

​    

## sed

TODO

   

## 参考

- https://www.zhihu.com/question/297858714 【sed和awk的区别】
- https://coolshell.cn/articles/9104.html 【sed简明教程】
- https://coolshell.cn/articles/9070.html 【awk简明教程】