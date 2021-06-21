---
title: git优雅的commit和日志查看
date: 2021-06-04
categories: [git]
tags: [git]
---

## commit命令

```bash
git commit #进入vi界面编辑
git commit -m "message" #直接输入剪短信息
git commit --amend  -m "new a" #修改最近一次的commit信息
git show ce05f93 #展示commit的变更详细信息
```

​    

## commit规范

```bash
<type>[optional scope]: <description>
<BLANK LINE>
[optional body]
<BLANK LINE>
[optional footer(s)]
```

- 标题行: 必填, 描述主要修改的type类型和desc简短描述
- body内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等详细注释，这是可选的
- 页脚注释: 放一些额外的信息，比如参考的issue、pr、review的人等

```bash
fix(dao): correct minor typos in code

see the issue for details
on typos fixed.

Reviewed-by: Z
Refs #133
```

`type` 提交类型

| type         | desc                                                         |
| ------------ | ------------------------------------------------------------ |
| feat         | 新特性                                                       |
| fix          | 修复bug                                                      |
| refactor     | 代码重构                                                     |
| docs         | 文档修改                                                     |
| style        | 代码格式修改                                                 |
| test         | 测试相关，增加测试用例，修改测试用例                         |
| chore        | 杂项，其它修改，比如依赖管理、CICD等，当然还可以指出具体的比如`ci:`  `build` |
| pref         | 代码优化，各种优化等                                         |
| sync         | 同步主线分支                                                 |
| merge/rebase | 合并分支                                                     |

`scope` 变更作用的层次和范围

route, component, utils, build,service,model...

`subject`  commit的简短描述，不超过50，建议符合  [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)

- 以动词开头，使用第一人称现在时，比如`change`，而不是`changed`或`changes`
- 第一个字母小写
- 结尾不加句号`.`

`body` commit message详细消息

阐述具体思路，可以分为多行, 建议符合 [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)

`footer`

一些备注，比如关闭的issue、pr、参考的一些链接等

```bash
Closes #123,#245,#992
```

`其他`

包含了 `!` 字符以提醒注意破坏性变更或者重大变更

```bash
refactor!: drop support for Node 6
```

​    

## commit日志查看

`log`查看commit日志

```bash
git log --oneline #以短格式显示
git log -2 #查看最近的2次提交
git log --graph --oneline #显示图形
```

`shortlog` 查看贡献者的commit信息

```bash
-s #统计用户的commit数量
-e #列出邮箱
git shortlog
```

`blame` 查看文件每一行的commit记录

```bash
git blame README.md
```

​    

## commit和tag

tag就是指想某个`commit`的的指针，类似于域名和IP的关系

因为某个`commit`号不好记，所以可以打个tag方便查找，而且通过tag更加容易进行版本管理，比如可以对某个commit标识为当前代码的一个特大版本号，直接通过tag就可以知道版本是哪几个

列出tag

```bash
git tag #列出所有标签
git tag -l 'v1*' #查找标签 查找以v1开头的标签
git ls-remote --tags origin  # 列出远程所有标签
```

创建tag

```bash
git tag v1.0 #为HEAD创建轻量标签,名字随便填(一般以v开头)
git tag v0.9 ce05f93 #为特定的commit创建tag
git tag -a v2.0 -m 'desc' #创建总重量型注释标签，会创建tag对象
```

删除tag

```bash
git tag -d v1.0 #删除本地tag
```

展示commit或则tag的变更内容

```bash
git show v2.0 #展示标签对应commmit的变更信息
git show ce05f93 #同理
```

切换到指定的commmit或则tag

```bash
git checkout v1 #切换到v1标签处
git checkout 332661b #切换到指定的commit
```

本地仓库的tag和远程仓库的tag

```bash
git push origin v1.0 #上传指定标签到远程仓库
git push origin --tags #把本地所有不在远程仓库的标签全部上传
git push origin :refs/tags/v1.0 #删除远程tag
```

​    

## 参考

[Conventional Commits](https://www.conventionalcommits.org/zh-hans/v1.0.0/)

[Commit message 和 Change log 编写指南](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

[如何规范你的 Git commit？](https://www.infoq.cn/article/fvrazhpyeljg9jsyddas)

