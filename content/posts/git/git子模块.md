---
title: submodule子模块
date: 2021-06-04
categories: [git]
tags: [git]
drfat: true
---

## git submodule子模块

子模块主要有如下几个作用:

- 将多个分离的项目统一放在一个文件夹下进行管理
- 将另外一个独立的项目导入到当前项目中，但是导入的项目不需要你自己来管理，由别人来管理

一个`git`仓库下面某个文件夹的来源可以跟本库的来源不同，这个文件夹连接着别的库，这样你可以让这个文件夹通过别的库来管理，主库唯一需要知道的信息是这个子模块当前最新的 `commit`，如果别人的项目更新了，主库可以决定自己是否需要跟新还是继续用旧版本。如果你一直不更新子模块，那么就永远是老的版本，子模块的更新需要你主动进行，**注意，主库是不追踪子模块的变化的，如果你修改了子模块，需要进子模块的目录下进行commit和push**

添加子模块到当前项目:

```
git submodule add https://github.com/biningo/biningo
```

如果子模块发生更改，仓库不会察觉到，需要我们手动进到子模块里去`commit`和`push`，如果子模块执行了`commit`，那么主库就会察觉到子模块发生了`commit`，提示执行`status`的时候就会提示子模块发生了变化，此时主库需要执行一次`commit`来提交更新

```
git submodule update #将子模块和远程仓库保持同步,也可以到子模块目录下手动merge或则rebase
clone`带有子模块的仓库不会拉取子模块相关代码，需要加上`--recurse-submodules`参数，或则执行`git submodule update
git clone --recurse-submodules https://github.com/chaconinc/MainProject
```