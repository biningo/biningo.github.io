---
title: git三大区域和远程仓库
date: 2021-06-05
categories: [git]
tags: [git]
---

## git三大区域

![](https://raw.githubusercontent.com/biningo/cdn/master/img/20171127144357194.jpg)

​    

## 工作区

工作区就是我们的本地目录

​     

## 暂存区

暂存区用于暂时存放我们工作区域的更改，比如我们工作区域修改了一个文件，此时就可以先放入暂存区

然后我继续修改这个文件......

改完之后我突然觉得文件这样修改不是很好，还是原来的比较好，想要恢复回原来的样子，此时暂存区就发挥作用了

我们可以将暂存区域的文件覆盖回工作区域，这样就又回到上次暂存的样子了，恢复之后此时暂存区的内容又和工作区域一致了

```bash
git add a.txt
git restore a.txt #将上次暂存的文件覆盖到工作区
git restore --staged a.txt #取消暂存 也就是清空暂存区
```

​    

## 本地仓库

如果确保此次修改就是最终版本，那么我们可以将暂存区域中的内容提交到本地仓库。此时暂存区的内容就被提交为一个版本，暂存区中的内容也会被清空

一次提交就相当于一次版本的发布，所有人都可以看到此版本所做的变更，并且以后可以回退到此版本

```bash
git commit a.txt  -m "new a"
git reset --hard HEAD^ #回退
git reset a112c #回退到特定的版本号
```

​    

## 三大区域变更

暂存区->工作区

```bash
git restore #将暂存区文件恢复到工作区
git restore --staged #取消暂存
git rm #直接删除暂存区和本地文件
git mv #同时修改暂存区和工作区的文件
```

版本库->工作区

```bash
#将工作区代码回滚到指定版本,此时暂存区内容也会清空
git reset <版本号>

#可以通过HEAD来指定 HEAD表示当前版本 HEAD^上一个版本 HEAD^^上上个版本....
git reset --hard HEAD
git reset --hard HEAD^
#~指定版本  假设v1 v2 v3 v4 v5 则现在HEAD在v5 命令之后:HEAD=v3
git reset --hard HEAD~2

#可以所有的版本号 以便回到过去还可以回到将来
git reflog
```

​    

## 状态查看

```bash
# M:表示有变更 
# A:表示已经添加到暂存库
# D:表示删除
git status -s
```

​    

## .gitignore文件

此文件由于告诉git哪些文件不需要进行git托管

- 以斜杠 `“/”` 结尾表示目录
- 以星号 `“*”` 通配多个字符
- 以问号 `“?”` 通配单个字符

```bash
*.a #忽略全部 .a 文件  包括子目录
!lib.a #但否定忽略 lib.a, 尽管已经在前面忽略了 .a 文件
build/ #忽略 build/ 文件夹下的所有文件
build #忽略build文件
doc/*.txt # 只忽略doc一级路径下的txt文件
doc/**/*.pdf # 忽略所有的 .pdf 文件 在 doc/ directory 下的
*.[oa] #忽略所有以".o"或".a"结尾
[0-9].txt #忽略0~9.txt结尾的文件
```

可以查看这个模板：[https://github.com/github/gitignore](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fgithub%2Fgitignore)   

​    

## 远程仓库

`clone`仓库

```bash
#clone指定在本地创建的关联分支 默认是main或者master
git clone -b dev https://github.com/ajd/test

# 递归克隆，如果项目包含子模块就非常有用
git clone --recursive git@github.com:xjh22222228/git-manual.git
```

`branch`查看保存在本地的远程仓库分支

```bash
git branch -r #查看远程仓库的分支 -rv则显示详细信息
git branch -a #查看所有分支(本地+远程)
git branch -vv #查看追踪关系
```

`remote`远程仓库相关操作

```bash
git remote #查看远程仓库 -v则显示链接
git remote add origin https://github.com/ajd/test #添加远程仓库 名字:origin
git remote rm origin #删除远程仓库
git remote set-url origin https://github.com/ajd/dsads #修改远程仓库的地址
```

`fetch`拉取远程仓库的代码

```bash
git fetch #将所有远程主机仓库全部更新到本地
git fetch origin #将指定远程仓库的所有分支的更新全部取回本地
git fetch origin dev #指定远程仓库中dev分支取到本地
```

下面几种命令可以将本地分支和远程分支建立 **追踪关系**

```bash
git push -u <远程主机> <本地分支>:<远程分支>
git push --set-upstream <远程主机> <本地分支>:<远程分支>
git branch --set-upstream-to=<远程主机名>/<远程分支名> <本地分支名>
git checkout -b <本地分支名> <远程主机名>/<远程分支名>
git branch <本地分支名> <远程主机名>/<远程分支名>

#将本地分支推送到远程仓库的指定分支 如果远程分支不存在则会在远端创建
git push <远程主机名>  <本地分支名>:<远程分支名> 
#相当于 git push origin dev:dev
git push origin dev
```

要从远程拉去代码很本地合并则只需要`pull`，但是也可以自己进行合并，即先`fetch`远程分支到本地，然后再将本地分支和拉去下来的远程分支代码进行合并即可，如下：

```bash
git pull origin dev #git fetch + git merge
```

推送`tag`

```bash
git push origin v1.0 #上传指定标签到远程仓库
git push origin --tags #把本地所有不在远程仓库的标签全部上传
git push origin :refs/tags/v1.0 #删除远程tag
```

