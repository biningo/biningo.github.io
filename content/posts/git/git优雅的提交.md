---
title: git优雅的提交
date: 2021-04-29
categories: [git]
tags: [git]
---

## git commit命令

```bash
git commit #进入vi界面编辑
git commit -m "message" #直接输入剪短信息
git commit --amend  -m "new a" #修改最近一次的commit信息
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
- 主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等详细注释，这是可选的
- 页脚注释: 放 `Breaking Changes` 或 `Closed Issues` 这也是可选的

### type

提交类型

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

### scope

影响范围

route, component, utils, build,service,model...

### subject

commit的简短描述，不超过50，建议符合  [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)

- 以动词开头，使用第一人称现在时，比如`change`，而不是`changed`或`changes`
- 第一个字母小写
- 结尾不加句号`.`

### body

commit的详细描述，阐述具体思路，可以分为多行, 建议符合 [50/72 formatting](https://link.zhihu.com/?target=https%3A//stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting)

### footer

一些备注, 比如关闭的`issue`，`pr`等，参考的一些链接等

```bash
Closes #123, #245, #992    
```

### 其它

包含了 `!` 字符以提醒注意破坏性变更的提交说明

```bash
refactor!: drop support for Node 6
```

​    

## 参考

[Conventional Commits](https://www.conventionalcommits.org/zh-hans/v1.0.0/)

[Commit message 和 Change log 编写指南](https://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)