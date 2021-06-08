---
title: git分支和工作流
date: 2021-06-05
categories: [git]
tags: [git]
---

## 分支管理

查看分支

```bash
git branch  #查看本地所有分支 -v则显示commit信息
git branch -r # 查看远端分支
git branch -a # 查看所有分支
```

创建分支

```bash
git branch <new> <old>
git branch dev #基于当前分支,创建dev分支
git branch feat dev #基于本地的dev分支，创建feat
git branch dev origin/dev #基于远程origin/dev分支，创建dev

git checkout -b dev #基于当前分支创建新分支,同时切换到新创建的分支
git checkout -b dev origin/dev #基于远程分支origin/dev中创建dev分支,同时切换
```

切换分支

```bash
git checkout - # 切换上一个分支
git switch dev #切换到一个已经存在的本地分支
git checkout dev #能切换到任意(本地分支/远程分支/某个commit/某个tag)
```

删除分支

```bash
git branch -d dev #删除分支 分支功能开发完毕之后就可以删除分支了
git branch -d -r origin/dev #删除本地保留的远端分支
git push origin -d dev #删除远端的dev分支
```

重命名分支

```bash
git branch -m old new
git branch -m dev develop #dev->develop
git branch -m main #当前分支->main
```

合并分支

```bash
git merge dev #合并dev分支到当前分支
git merge a b #将b合并到a
git rebase #todo
```

​     

## 什么是工作流

工作流就是工作流程，让大家有效地合作，通过规定的工作流程进行分支开发和代码合并

​    

## 集中式工作流

只有一个分支，每个人的代码全部提交到此分支上

<img src="https://raw.githubusercontent.com/biningo/cdn/master/img/Basic-git-workflow-3.png" style="zoom: 33%;" />

​    

## 特性分支工作流

给某个功能特性或修复某个bug开辟一个分支进行开发，这样就可以独自来发自己的功能而不被其它功能的代码污染，只需要最后再合并到主干分支即可

bug修复分支也都如此，有bug则立即开辟一个bug修复分支进行修复，修复完毕之后再`merge`到`master`分支

![](https://raw.githubusercontent.com/biningo/cdn/master/img/feature-branch-with-develop-git-workflow-2.png)

​    

## git flow工作流

项目长期存在两个分支

- `master`  用于存放对外发布的版本，任何时候在这个分支拿到的，都是稳定的分布版
- `develop`  用于日常开发，存放最新的开发版

项目存在三种短期分支，短期分支在使命完成之后就可以删除了

- `feature` 各种功能分支
- `hotfix` 补丁分支
- `release` 预发布分支，用于对代码进行最终测试

![](https://raw.githubusercontent.com/biningo/cdn/master/img/GitFlow-git-workflow-2.png)

​    

## github工作流

github采用的`pull request`和`fork` 的方式进行开发

github上的开源代码任何人都可以贡献代码，只需要`fork`项目到自己的仓库，然后基于原仓库的分支创建一个自己的分支并在自己仓库的分支上面进行修改，然后可以向原分支发起一个`pull request`

原项目的负责人需要对`pr`进行`review`，如果觉得不合适则可以让贡献者继续修改或则直接关闭pr，如果觉得可以进行合并操作合并贡献者的代码，pr在完成之后贡献者就可以删除pr相关的分支了

​    

## 参考

[5种Git工作流改善你的开发流程](https://juejin.cn/post/6864753221894668302)

[Git 工作流程](http://www.ruanyifeng.com/blog/2015/12/git-workflow.html)

