---
title: 什么是git
date: 2021-06-05
categories: [git]
tags: [git]
draft: true
---

## 什么是git

- 版本控制工具
- 分布式

git在中心仓库保存所有的版本信息，同时也会在所有用户的本地也会保存所有版本

这样就做到了 **分布式**，git在中心仓库挂了之后依然可以在本地提交版本，等中心仓库恢复之后再一起`push`上去

使用git分布式版本管理工具的最重要的就是要解决多人协作的 **版本冲突** 问题，在使用git的时候，时刻需要保持一个理念

- 你的每次commit都是一个版本

- 提交版本的时候就会标注上 **user.name、user.email** 指示出这是谁提交的版本

- 只要是一个版本，那么在以后就都可以回滚到这个版本中 

​      

## 参考

