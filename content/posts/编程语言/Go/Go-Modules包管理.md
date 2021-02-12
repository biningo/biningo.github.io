---
title: Go Modules包管理
date: 2021-02-12
categories: [编程语言]
tags: [Go]
---

## Go Modules环境变量

要开启`Go Modules`管理，需要设置一个环境变量：

```bash
GO111MODULE="on" #默认是开启的
```

​    

## go.mod文件

```go
//1、每个模块都有一个名字 通常通过 go mod init 指定
module github.com/biningo/go-play 
//2、指定go版本
go 1.15 

//3、指定依赖的库地址 <url> <version> 形式
require (
    github.com/gin-gonic/gin v1.6.3
)

//4、replace 替换 require 中声明的依赖，使用另外的依赖及其版本号 不经常使用
replace github.com/gin-gonic/gin v1.6.3 => github.com/gin-gonic/gin v1.6.3

//5、exclude 排除某些版本
exclude ithub.com/gin-gonic/gin v1.5.0
```

**模块名字**主要有如下几个作用：

- **作为模块的标识**

- **作为模块的 import path**

    当其他项目引用这个模块下的 `package` 时都会以该 import path 作为共同的前缀，自己的项目引用自己项目的包也必须是这个前缀，代表了这个项目，然后前缀下就可以根据路径来引入包了

开启`mod`模式之后，所有的`go get`命令拉取的包都会放到`$GOPATH/pkg/mod`路径下，如果是在项目下面执行`go get`则也会同时将响应的依赖信息记录到`go.mod`文件

`go mod`的`replace`命令主要用来替换原来的包，使用场景有如下几个

- **单纯的替换包（用处不大）**

- **替换无法下载的包**

    由于网络问题，有些包无法下载，比如 `golang.org` 下的包，而这些包在 GitHub 都有镜像，此时就可以使用 GitHub 上的包来替换

​    

## go mod包的版本

Go Modules的版本其实就是项目的`tag` ，如果没有`tag`则下载的时候会自动根据最新的`commit`生成一个版本

​        

## go mod命令

初始化一个项目，也就是创建项目的`go.mod`文件，该文件记录了所有的依赖信息以及go的版本

```bash
go mod init github/biningo/bstree #一般以github仓库地址为项目名字
```

`go.mod`文件记录的依赖可能在本地没有，执行如下命令即可下载所有本地仓库不存在的依赖

```bash
go mod download
go list -m all #此命令用于列出mod所有依赖的模块名字  如果本地不存在也会进行自动下载
go list -m -json all #以json格式列出
go list -m -json #不加all则只会列出自己项目的模块信息
```

`go.mod`可能会记录一些没有用到的包，可以用如下命令来清除这些包，此**命令也会自动下载本地不存在的依赖库**

```bash
go mod tidy
```

可以将依赖的包的代码都复制到本项目下，可以使用如下命令

```bash
go mod ventor
```

​    

## go get命令

`go get`命令主要用于下载依赖的代码

```bash
go get github.com/gin-gonic/gin #拉取、编译安装 如果包以及依赖包存在则不会进行下载
go get -d github.com/gin-gonic/gin  # -d只拉取 不编译安装
go get -u github.com/gin-gonic/gin 	#-u下载最新版 不管原来是否下载了都会下载 包括依赖的包都会进行重新下载安装
go get -v github.com/gin-gonic/gin #-v被构建的代码包名字
go get -x github.com/gin-gonic/gin #-x 打印出用到的命令
go get -u #可以下载更新所有依赖到最新版本
```

​    

## 参考

https://www.infoq.cn/article/xyjhjja87y7pvu1iwhz3