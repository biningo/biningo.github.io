---
title: Go-Modules包管理
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

## go.sum文件

该文件记录格式如下：

```bash
# <module> <version>/go.mod <hash>
github.com/gin-gonic/gin v1.6.3/go.mod h1:ahKqKTFpO5KTPHxWZjEdPScmYaGtLo8Y4DMHoEsnp14=
```

后面的`hash`就是`go get`命令生成的对代码压缩包的`hash`值（请看下面go get流程解析部分）

如果我们拿到一个开源项目，进行`build`下载依赖包进行构建的时候，如果本地缓存了相关的代码库，则取对应代码库的`zip`压缩包的`hash`值与当前项目的`go.sum`对应依赖的`hash`进行比较，如果一致则表示本地缓存的代码库和项目期望的代码库版本一致，则可以构建，如果不一致则表示本地缓存的版本不是我想要的则构建失败，需要下载项目指定的依赖版本进行构建

**go.sum存在的意义在于，我们希望别人或者在别的环境中构建当前项目时所使用依赖包跟go.sum中记录的是完全一致的，从而达到一致构建的目的**

用`go get`等其他命令进行下载依赖库的时候会将`go.mod`、`go.sum`进行同步更新

​                

## go包的版本

Go Modules的版本其实就是项目的`tag` ，如果没有`tag`则会自动生成`v0.0.0-commit日期-commitID`

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

`go get`命令主要用于下载依赖的代码，开启`go mod`之后（现在go版本已经默认开启了），所有的`go get`命令下载的依赖都会下载到`$GOPATH/pkg/mod`下，`go get`后面跟着 **模块的名字(一般模块的名字就是代码仓库的链接)**  然后`go`就会根据这个模块名字去自己的包管理库去寻找，该库记录了go项目的所有信息，包括项目的注释文档，包括代码仓库地址（一般是github）和模块的名字，第一次如果库里没有则go会自动获取仓库的信息和代码加入到自己的库里

```bash
go get github.com/gin-gonic/gin #拉取、编译安装 如果包以及依赖包存在则不会进行下载
go get -d github.com/gin-gonic/gin  # -d只拉取 不编译安装
go get -u github.com/gin-gonic/gin 	#-u下载最新版 不管原来是否下载了都会下载 包括依赖的包都会进行重新下载安装
go get -v github.com/gin-gonic/gin #-v被构建的代码包名字
go get -x github.com/gin-gonic/gin #-x 打印出用到的命令
go get -u #可以下载更新所有依赖到最新版本
```

`go get`可以手动指定版本

```bash
go get -u github.com/gin-gonic/gin@v1.6.3 #@xxx
go get -u github.com/gin-gonic/gin@master #拉指定分支也可
go get -u github.com/gin-gonic/gin@v1 #@v1则代表此大版本的最新版本 v1.6.3 @v1.1则表示 v1.1的最新的版本比如v1.1.4最新
go get -x github.com/gin-gonic/gin@latest #最新的tag 默认不加就是这个选项
```

`go get`的过程如下：

- 将该依赖包下载到本地缓存目录`$GOPATH/pkg/mod/cache/download`，该依赖包为一个后缀为`.zip`的压缩包，如`v1.0.0.zip`
- 下载完成后会对该`.zip`包做哈希运算，并将结果存放在后缀为`.ziphash`的文件中，如`v1.0.0.ziphash`
- 然后解压放到对应的`$GOPATH/mod`下的目录下，并且编译代码将生成的二进制文件放到`$GOPATH/bin`下

> 注意分清楚`$GOROOT`和`$GOPATH`的关系：
>
> - 前者是**go语言本身的安装路径**，包括标准库代码，各个二进制文件工具等
> - 后者用于存放第三方`go get`下来的第三方代码和二进制文件，该路径分别包含了
>     - bin（第三方代码编译之后的二进制可执行文件）
>     - src（没什么用，以前老版本用于项目存放代码的地方）
>     - pkg（第三方库的代码文件）

​    

## go list命令

用于列出当前项目的`go mod`文件依赖了哪些模块，如果相应模块没有拉去到本地或则安装，则会自动执行`go get`命令进行下载安装，**主要作用就是拿到一个陌生的项目，然后工具该项目的 go.mod 相关依赖信息安装依赖**

```bash
go list all #列出依赖了的标准库
go list -f {{.xxxx}} #指定输出格式（包括标准库 不包括mod模块）
go list -json #以json格式输出项目依赖信息(包括标准库 不包括mod模块)
go list -m all  #查看所有依赖以及间接依赖mod模块当前版本（不包括标准库）
go list -u -m all #查看所有依赖以及间接依赖当前版本和可升级版本
go list -m -versions #github.com/gin-gonic/gin 查看某些模块的所有版本
```

​    

## 参考

https://www.infoq.cn/article/xyjhjja87y7pvu1iwhz3

https://studygolang.com/articles/25658

https://my.oschina.net/renhc/blog/3171035