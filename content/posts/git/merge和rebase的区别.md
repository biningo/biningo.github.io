---
title: merge和rebase的区别
date: 2021-04-29
categories: [git]
tags: [git]
---

## merge和rebase的区别

假如下面的分支如下

```bash
       E---F dev
      /
 A---B---C---D master
```

### 使用rebase的场景

此时dev为了和master保持同步，则在dev中使用`rebase`操作，rebase就是寻找公共父节点

```bash
       C---D--E--F dev
      /
 A---B---C---D master
```

实际上现在的分支图变成了这样

```bash
       		  E--F dev
      		 /
 A---B---C---D master
```

此时在`master`分支中不管是执行`rebase`还是`merge`都是一条线，其实就是将master指针移动到dev指针处

```bash
A---B---C---D---E---F dev,master
```

### 使用merge的场景

dev为了和master保持同步，于是使用`merge`

```bash
       E---F---G dev
      /		  /
 A---B---C---D master
```

现在master使用`merge`合并dev

```bash
       E---F---G dev
      /		  / \
 A---B---C---D---H  master
```

此时master又开发了几个版本，dev也开发了几个版本

```bash
       E---F---G---L---M dev
      /		  / \
 A---B---C---D---H---I---J  master
```

此时dev又要进行和master同步

```bash
       E---F---G---L---M----K dev
      /		  / \		  /
 A---B---C---D---H---I---J  master
```

此时dev需要合并到master中

```bash
       E---F---G---L---M----K dev
      /		  / \		  / \
 A---B---C---D---H---I---J---P  master
```

看上面merge的图，分支一多就显的很乱

​     

## rebase的其它作用

rebase还可以用于合并多个提交，有可能你本地提交了多个版本，但是合并到master中只想压缩为一个版本，于是就可以使用`rebase -i`目录进行压缩

```bash
git rebase -i HEAD^^ #将前两个版本压缩为一个 必须找到带压缩边界的前一个版本
git rebase -i --root #如果到根节点了的话可以使用这个
git rebase -i HEAD~4 #也可以使用这种方式
```

关于合并，有几个符号需要注意:

```bash
p, pick = use commit
r, reword = use commit, but edit the commit message
e, edit = use commit, but stop for amending
s, squash = use commit, but meld into previous commit **
f, fixup = like “squash”, but discard this commit’s log message
x, exec = run command (the rest of the line) using shell
d, drop = remove commit
```

