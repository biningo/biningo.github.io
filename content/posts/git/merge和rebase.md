---
title: merge和rebase
date: 2021-06-04
categories: [git]
tags: [git]
draft: true
---

## merge命令

合并代码并且在当前分支上将合并的内容建立一个新的commit，同时会保留另外分支的commit信息，相当于出现分叉

```bash
git merge dev #此时会进入编辑器
git merge dev -m "commit msg" #-m直接指定
```

`--squash` 将其他分支的代码合并并且不提交，需要手动commit一次，此方式不会出现分叉分支，相当于将其他分支的commit都压缩为一个

```bash
git merge dev --squash
git commit -m "E"
```

​         

## rebase命令

rebase寻找两个分支的公共节点，然后基于目标分支的最新的commit将自己的commit再次提交一边

```bash
git rebase master
```

如果rebase发生冲突，则需要手动解决冲突，然后继续rebase

```bash
git rebase --continue
```

使用如下命令终止提交

```bash
git rebase --abort
```

​     

## rebase压缩多个commit

rebase还可以用于合并多个提交，比如你本地提交了多个版本，但是push到远程仓库的只想压缩为一个版本，于是就可以使用`rebase -i`进行压缩

```bash
git rebase -i HEAD^^ #将前两个版本压缩为一个 必须找到带压缩边界的前一个版本
git rebase -i HEAD~4 #也可以使用这种方式
git rebase -i --root #如果到根节点了的话可以使用这个
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

```bash
#将前3个commit进行选择
git rebase -i HEAD~3
--------------------
pick 7689e4f b
s 1243482 d
s 2b4a6fe e
-----------------
将d、e合并到b中  越上面的是越早提交的记录
```

​           

## merge和rebase的区别

假如下面的分支如下

```bash
       E---F dev
      /
 A---B---C---D master
```

> rebase

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

> merge

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

## 参考

[彻底搞懂 Git-Rebase](http://jartto.wang/2018/12/11/git-rebase/)

