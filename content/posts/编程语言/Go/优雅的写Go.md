---
title: 优雅的写Go
date: 2021-02-14
categories: [编程语言/Go]
tags: [Go]
---

> **跟新中.....**

## 静态代码检查工具

### 1、goimports

`goimports` 是 Go 语言官方提供的工具，主要有如下几个作用：

- 自动**格式化 Go 语言代码**
- 对所有引入的包进行管理，自动增删依赖的包引用、将依赖包按字母序排序并分类

`goimports` 就是等于 `gofmt` 加上依赖包管理

用法和`go fmt`差不多，这里不再赘述，**另外，在CI/CD中不应该加入这些检查，因为这是开发者的本职工作，应该由开发者来规范代码**

```bash
goimport -w . #格式化 并且修改原文件
```

### 2、go vet和golint

**代码语法检查、代码风格检查**，官方提供，没什么用

### 3、golangci-lint

强大的go开源静态代码分析，用于`CI`防止不规范代码合并，主要有几个用途：

- 代码规范
- 代码风格统一
- 语法错误、冗余语法等

```bash
golangci-lint run ./...
golangci-lint run dir1 dir2/... dir3/file1.go
```

具体见官网：https://golangci-lint.run/usage/performance

​    

## go代码报告评分平台

https://goreportcard.com

​        

## 参考

https://www.jianshu.com/p/ca38dcdaf6bb

https://supereagle.github.io/2019/10/03/golang-lint/