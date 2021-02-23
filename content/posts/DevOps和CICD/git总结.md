---
title: git总结
date: 2021-02-08
categories: [DevOps和CICD]
tags: [git]
---

## 什么是git

- 版本控制工具
- 分布式

**git在中心仓库保存所有的版本信息**

**同时也会在所有用户的本地也会保存所有版本**

这样就做到了 **分布式**，git在中心仓库挂了之后依然可以在本地提交版本，等中心仓库恢复之后再一起`push`上去

使用git分布式版本管理工具的最重要的就是要解决多人协作的 **版本冲突** 问题

在使用git的时候，时刻需要保持一个理念：

- **你的每次commit都是一个版本**

- 提交版本的时候就会标注上 **user.name、user.email** 指示出这是谁提交的版本

- 只要是一个版本，那么在以后就都可以回滚到这个版本中 

​    

## 三大区域

![](https://raw.githubusercontent.com/biningo/cdn/master/img/20171127144357194.jpg)

**工作区：** 直接写代码，修改文件的地方

**暂存区：** 将工作区的当前映象暂存起来以准备提交到`版本库`中，简单来说就是可以在提交最终的版本之前还可以继续修改反悔等，举个简单的例子：

- 我删除了`a`，并且`add`到暂存区
- 现在我添加了`b`，添加完之后就觉得还是`a`好，于是反悔了，将上次暂存区中的文件又覆盖了工作区，也就是说现在又回到了`a`
- 最终我觉得就`a`了，不再修改了，于是就`add+commit`将最终的版本提交了

**版本库：** 存放最终的版本

**远程仓库：** 供多人协作用，本地仓库的版本最终需要`push`到远程仓库，并且在`push`之前需要和远程仓库保持 **同步**  这要所有人才可以看到版本信息

​    

## 三大区域变更

**暂存区->工作区**

```bash
#1、将暂存区文件覆盖工作区
git restore <file> ...
#2、取消暂存
git restore --staged <file>...
#3、直接删除暂存区和本地文件 等同于:手动删除文件 + git add file
git rm a
#4、直接同时修改暂存区和本地文件名字 等同于:mv a b + git rm a + git add b
git mv a b
```

**版本库->工作区**

```bash
#1、将工作区代码回滚到指定版本 此时暂存区内容也会清空
git reset --hard <版本号>

#2、可以通过HEAD来指定 HEAD表示当前版本 HEAD^上一个版本 HEAD^^上上个版本....
git reset --hard HEAD
git reset --hard HEAD^
git reset --hard HEAD~2 #假设v1 v2 v3 v4 v5 则现在HEAD在v5 命令之后:HEAD=v3

#3、版本号可以通过如下命令获得
git log --oneline #只记录HEAD之下的版本号
git reflog #可以所有的版本号 以便回到过去还可以回到将来
```

​    

## 分支管理

```bash
git branch #查看本地所有分支 -v则显示详细信息
git branch dev #创建新的dev分支
git switch dev #切换分支 （注意：切换前需要先提交变更）
git checkout -b dev #创建并且切换 上面两条命令的合并
git branch -d dev #删除分支 分支功能开发完毕之后就可以删除分支了
git merge dev #合并dev分支到当前分支 （注意分支冲突问题 需要手动解决然后commit）
```

创建分支可以从其他分支中创建，如下：

```bash
git branch dev dev2 #基于dev分支，创建dev2
git branch origin/dev dev2 #基于远程分支origin/dev中创建dev2分支
```

​            

## 远程仓库

```bash
git branch -r #查看远程仓库的分支 -rv则显示详细信息
git branch -a #查看本地+远程所有分支
git branch -vv #查看本地和远程对应的分支
```

```bash
git remote #查看远程仓库 -v则显示链接
git remote add origin https://github.com/ajd/test #添加远程仓库 命名:origin
git remote rm origin #删除远程仓库
git remote set-url origin https://github.com/ajd/dsads #修改远程分支的url
```

```bash
git fetch #将所有远程主机仓库全部更新到本地
git fetch origin #将指定远程主机origin的所有分支的更新全部取回本地
git fetch origin dev #指定远程仓库origin中dev分支取到本地
```

注意，远程仓库和本地仓库的分支是两个不同的分支，需要将本地分支和远程仓库分支建立 **追踪关系**

```bash
#1、将本地分支推送到远程仓库的指定分支 如果远程分支不存在则会在远端创建
git push <远程主机名>  <本地分支名>:<远程分支名> 

#2、git push origin dev:dev
git push origin dev

#3、-u则表示将当前本地分支和远端的origin/dev分支建立追踪关系 --set-upstream的缩写形式，以后直接执行 git push即可将当前分支push到origin/dev分支
git push -u origin dev
```

要从远程拉去代码很本地合并则只需要`pull`，但是也可以自己进行合并，即先`fetch`远程分支到本地，然后再将本地分支和拉去下来的远程分支代码进行合并即可，如下：

```bash
git pull origin dev #git fetch origin origin dev + git merge origin/dev
```

​    

## rebase和merge分支合并

### 1、rebase合并多个commit

`rebase -i`可以用于将多个`commit`合并为一个`commit` ，**但是不要将已经push到仓库的commit合并**

```bash
#将当前和的3次commit进行合并为一个commit 包括HEAD
#如果写具体的版本号，则表示将该版本号之前的commit都合并为一个
#将其他的commit的pick改为s，只保留最上面的pick
git rebase -i HEAD~3
```

下面展示一个案例：`v1->v2->v3->v4->v5`

```bash
#1、将v5~v2合并为一个commit v2345
git rebase -i [v1的commit ID]
-------------------------
pick v2
s v3
s v4
s v5

#2、将v5 c4合并 v3 v2合并 也可以分开合并也可  （一个pick就是一个commit）
git rebase -i [v1的commit ID]
------------------------
pick v2
s v3
pick v4
s v5

#3、将HEAD之后的2个(包括HEAD)合并为1个
git rebase -i HEAD~2
```

但是上面rebase合并commit记录的时候需要找到待合并commit的前一个commit，那么第一次的commit如何合并呢，第一次commit是没有前一个commit的，他就是第一个，这种情况我们可以使用如下命令来将多个commit与第一个commit合并

```bash
git rebase -i --root
```

### 2、rebase分支变基

```bash
git rebase master #将当前分支和master分支进行变基合并
```

注意，如果`dev`已经基于`master`变基过了，那么`main`再将`dev`进行`merge`的时候，就不会出现分叉，直接就是一条线，因为`dev` rebase之后就相当于在`master`最新的上面进行开发，`dev`会将所有的commit再基于master最新的commit再进行commit一次，此时commit版本号都会发生变化，之后`master`进行merge自然就不会出现分叉了

### 3、解决合并冲突

如果是`rebase`冲突了则需要到响应的文件进行修改，改完之后再进行`commit`，然后执行命令`git rebase --continue`进行继续rebase

如果是`merge`则也是一样处理的

我们还可以借助冲突解决工具来解决冲突，比如：**beyond compare**

​    

## tag管理

tag就是指想某个`commit`的的指针，类似于 **网址和IP** 的关系，因为某个`commit`号不好记，所以可以打个`tag`方便查找，而且通过`tag`更加容易进行版本管理了，直接通过`tag`就可以知道版本是哪几个

```bash
git tag #列出所有标签
git tag v1.0 #创建轻量标签
git tag -a v2.0 -m 'desc' #创建注释标签
git tag -l 'v1*' #特点模式查找标签 查找以v1开头的标签
git show v2.0 #展示标签信息
git tag v0.9 ce05f93 #上面默认是在最新的HEAD上打标签，也可以基于历史版本打tag
#删除标签麻烦点，先本地删除，再远程删除
git tag -d v1.0 #删除本地tag
git push origin :refs/tags/v1.0 #删除远程tag
git push origin v1.0 #上传指定标签到远程仓库
git push origin --tags #把本地所有不在远程仓库的标签全部上传
git branch dev v1.0 #从v1.0标签上创建dev分支
#切换到v1标签处 这时工作目录下面才是标签的内容代码 注意标签内容无法改动和commit
git checkout v1
```

​    

## git工作流

### 1、集中式工作流

只有一个分支，代码全部提交到此分支上

<img src="https://raw.githubusercontent.com/biningo/cdn/master/img/Basic-git-workflow-3.png" style="zoom: 33%;" />

### 2、特性分支工作流

给每个功能特性都开辟一个分支进行开发，这样就可以独自来发自己的功能，最后再合并到主干分支即可，bug修复分支也都如此，有bug则立即开辟一个bug修复分支进行修复，修复完毕之后再`merge`到`master`分支

![](https://raw.githubusercontent.com/biningo/cdn/master/img/Feature-Branch-git-workflow-4.png)

`develop`用于合并多个子功能，然后合并到`master`

![](https://raw.githubusercontent.com/biningo/cdn/master/img/feature-branch-with-develop-git-workflow-2.png)

### 3、git flow工作流

项目长期存在两个分支：

- `master` ：用于存放对外发布的版本，任何时候在这个分支拿到的，都是稳定的分布版
- `develop` ：用于日常开发，存放最新的开发版

项目存在三种短期分支：

- `feature`：各种功能分支
- `hotfix`：补丁分支
- `release`：预发布分支，用于对代码进行**最终测试**保证最终是没有bug的

短期分支完成之后就可以删除了

![](https://raw.githubusercontent.com/biningo/cdn/master/img/GitFlow-git-workflow-2.png)

### 4、github的pull request和fork工作流

github上的开源代码任何人都可以贡献代码，只需要`fork`项目到自己的仓库，可以选择直接在`master`分支上进行修改然后发起一个 **pull request**，源项目的负责人就可以对这个`request`进行`review`，如果觉得需要修改则让贡献者继续修改，如果觉得可以合并了则进行`merge`操作合并贡献者的代码，贡献者也可以选择重新创建一个分支然后再进行**pull request** 

​    

## gitignore文件

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

## git commit规范

`commit message`的格式如下：

```http
<type>(<scope>): <subject>
fix(DAO):用户查询缺少username属性 
feat(Controller):用户查询接口开发
```

**type(必须)：** 用于说明 git commit 的类别，只允许使用下面的标识

```bash
feat：新功能（feature）
fix/to：修复 bug，可以是 QA 发现的 BUG，也可以是研发自己发现的 BUG。
	fix：产生 diff 并自动修复此问题。适合于一次提交直接修复问题
	to：只产生 diff 不自动修复此问题。适合于多次提交。最终修复问题提交时使用 fix
docs：文档（documentation）
style：格式（不影响代码运行的变动）
refactor：重构（即不是新增功能，也不是修改 bug 的代码变动）
perf：优化相关，比如提升性能、体验
test：增加测试
chore：构建过程或辅助工具的变动
revert：回滚到上一个版本
merge：代码合并。
sync：同步主线或分支的 Bug
```

**scope(可选)**：用于说明commit 影响的范围，比如数据层、控制层、视图层等等，不同的项目不一样

**subject(必须)：** subject 是 commit 目的的简短描述，不超过 `50` 个字符。

- 中文/英文
- 结尾不加句号或其他标点符号

下面我们再来看看`git commit`的相关命令

```bash
git commit -m "xxxx"
git commit --amend #修改最近一次的commit的信息
git commit -am "xxx" #免去了 git add 操作，直接commit     
```

如果要修改`commit message`信息，可以通过上面的`rebase`命令来修改，只需要修改`message`部分即可

```bash
git rebase -i HEAD^^     
```

​     

## git submodule子模块

子模块主要有如下几个作用:

- 将多个分离的项目统一放在一个文件夹下进行管理
- 将另外一个独立的项目导入到当前项目中，但是导入的项目不需要你自己来管理，由别人来管理

一个`git`仓库下面某个文件夹的来源可以跟本库的来源不同，这个文件夹连接着别的库，这样你可以让这个文件夹通过别的库来管理，主库唯一需要知道的信息是这个子模块当前最新的 `commit`，如果别人的项目更新了，主库可以决定自己是否需要跟新还是继续用旧版本。如果你一直不更新子模块，那么就永远是老的版本，子模块的更新需要你主动进行，**注意，主库是不追踪子模块的变化的，如果你修改了子模块，需要进子模块的目录下进行commit和push**

添加子模块到当前项目:

```bash
git submodule add https://github.com/biningo/biningo
```

如果子模块发生更改，仓库不会察觉到，需要我们手动进到子模块里去`commit`和`push`，如果子模块执行了`commit`，那么主库就会察觉到子模块发生了`commit`，提示执行`status`的时候就会提示子模块发生了变化，此时主库需要执行一次`commit`来提交更新

```bash
git submodule update #将子模块和远程仓库保持同步,也可以到子模块目录下手动merge或则rebase
```

`clone`带有子模块的仓库不会拉取子模块相关代码，需要加上`--recurse-submodules`参数，或则执行`git submodule update`

```bash
git clone --recurse-submodules https://github.com/chaconinc/MainProject
```

​    

## 参考

- https://juejin.cn/post/6864753221894668302
- http://www.ruanyifeng.com/blog/2015/12/git-workflow.html
- https://www.infoq.cn/article/fvrazhpyeljg9jsyddas
- https://github.com/521xueweihan/git-tips
