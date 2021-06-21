---
title: vim使用教程
date: 2021-02-21
categories: [Linux]
tags: [vim,Linux] 
---

光标移动(命令模式)

```bash
n+空格 #在当前行中移动n个字符
n+Enter #移动到距离当前行后n行
H/M/L #移动到屏幕第一行/中间行/最后一行
gg/G/#移动到当前文件第一行/最后一行
nG/ngg #移动到特定的第n行
w/nw #向前 移动1个单词/移动n个单词
b/nb #向后 移动1个单词/移动n个单词
0/$ #移动到行的开头/结尾
h/j/k/l #左/下/上/右
```

文本编辑删除复制粘贴(命令模式)

```bash
i/a/I/A	#在 光标前/光标后/行首/行尾 插入文本
o/O(字母O) #在光标下/上方新开一行
x/X #命令模式下删除光标后/前的单个字符
r/R #替换字符/复写模式,连续替换
d0/d$ #删除到 行首/行尾
dgg/dG #删除到 文件头/文件尾
dd/ndd #删除该行/删除该行下的n行
yy/nyy #复制该行/n行
p/P #当前行下/上面粘贴
u/ctrl+r #撤销上次操作/撤销反悔,重做上次撤销
. #复制前一个动作
```

文档检索

```bash
/search_text	#在文档后面的部分搜索
?search_text	#在文档前面的部分搜索
n/N	#往前/后移动检索到的关键字
:noh #取消检索的高亮

:%s/abc/aaa	#检索第一个 “abc” 字符串并将其替换成“aaa”
:%s/abc/aaa/g #检索并将所有的“abc”,替换为“aaa”
:%s/original/replacement/gc	#替换前先询问
:%n1,n2s/A/B/g #在n1-n2行之间检索替换

:!command #暂时离开执行命令
:set nu #设置行号
:set nonu#取消行号
```

可视模式

```bash
v #逐字可视模式
V #逐行可视模式
```

保存文件

```bash
:w new_file #另存为 不会退出
ZZ # :wq
ZQ # :q!
```

​    

参考

https://linux.cn/article-8144-1.html