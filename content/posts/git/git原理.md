---
title: git原理
date: 2021-04-30
categories: [git]
tags: [git]
draft: true
---

## git三大对象

git三大对象分别为:

- **blob** 
- **tree**
- **commit**

下面来讲述这些对象的细节:

- `blob` 记录了文件的实际内容，每次`add`都会为每个文件生成一个blob，暂存区有个`index`指针会指向最新add到暂存区的几个blob

每次进行commit时都会生成两个对象: tree和commit

- `tree` 记录了`index`指针指向的几个`blob`的hash值还有对应的文件信息和文件权限

- `commit`记录了此次commit的作者信息和对应的`tree`节点，同时还会记录上一个commits

​    

## 参考

[这才是真正的Git——Git内部原理](https://juejin.cn/post/6844904019245137927)

[三道 google 风格 git 面试题及其解答](https://juejin.cn/post/6844903876743659534)

