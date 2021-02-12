---
title: Go命令行
date: 2021-02-12
categories: [编程语言]
tags: [Go]
---

## go build

将代码编译为相关平台的可执行文件，只需要编译带有`main`的入口文件即可

```bash
go build #会寻找当前目录下main入口文件然后进行编译
go build -o main  #指定生成可执行文件的名字
go build mymain.go #也可以编译指定的go文件
```

​    

## go env

用于管理go的环境变量相关信息，go相关环境变量也可在`.bashrc`等文件里面设置，优先级高

```bash
go env #打印所有go的环境变量
go env GOPROXY #打印某个环境变量的值
go env -json #json格式输出
go env -w GOPROXY=https://goproxy.cn,direct #修改某个值 这里设置了中国代理，direct表示如果代理没有则直接走go官网，可以设置多个代理网站，用逗号分割
```

​    

## go fmt和gofmt

`go fmt`是对`gofmt`的封装，直接使用`gofmt`即可，格式化如果不加`-w`是不会改变源代码的，所以最常用的就是：

```bash
gofmt -w ./ #格式化当前项目，并且会修改原文件而不是输出到控制台
```

还有一些其他命令，看看就行

```bash
gofmt test.go #格式化单个文件
gofmt ./viper/ #格式化整个目录文件
gofmt -l ./viper/ #列出哪些文件格式化前后会出现不同(只是列出 不进行格式化)
gofmt -w ./viper/ #执行格式化 并且写入源代码
```

​    

## go get

最重要的一个工具，用于获取go依赖，类似于`pip、maven、npm`等 go的包管理采用`go mod`的方式，每个项目都必须有一个`go.mod`依赖声明文件，里面列出了自己的项目依赖了哪些项目，以及依赖项目的版本信息，在项目下面执行`go get  [url]`即可下载依赖并且将依赖项自动添加到`go.mod`文件中

开启`go mod`之后（现在go版本已经默认开启了），所有的`go get`命令下载的依赖都会下载到`$GOPATH/pkg/mod`下

> 注意分清楚`$GOROOT`和`$GOPATH`的关系：
>
> - 前者是**go语言本身的安装路径**，包括标准库代码，各个二进制文件工具等
> - 后者用于存放第三方`go get`下来的第三方代码和二进制文件，该路径分别包含了
>     - bin（第三方代码编译之后的二进制可执行文件）
>     - src（没什么用，以前老版本用于项目存放代码的地方）
>     - pkg（第三方库的代码文件）

`go get`其实干了下面几件事情：

- `git clone`拉代码到`pkg/mod`文件夹下 （下载代码）
- 编译代码，生成二进制可执行文件，然后放到`$GOPATH/bin`目录下（安装）

主要有如下几个命令行选项：

```bash
go get github.com/gin-gonic/gin #拉取、编译安装 如果包以及依赖包存在则不会进行下载
go get -d github.com/gin-gonic/gin  # -d只拉取 不编译安装
go get -u github.com/gin-gonic/gin 	#-u下载最新版 不管原来是否下载了都会下载 包括依赖的包都会进行重新下载安装
go get -v github.com/gin-gonic/gin #-v被构建的代码包名字
go get -x github.com/gin-gonic/gin #-x 打印出用到的命令
```

go拉去代码的时候必须指定代码的提交版本，如果不指定，则默认是最后一个版本，比如最后一个`tag`，如果没有`tag`则是最后一次`commit`，也可以手动指定版本

```bash
go get -u github.com/gin-gonic/gin@v1.6.3 #@xxx
go get -u github.com/gin-gonic/gin@master #拉指定分支也可
go get -u github.com/gin-gonic/gin@v1 #@v1则代表此大版本的最新版本 v1.6.3 @v1.1则表示 v1.1的最新的版本比如v1.1.4最新
go get -x github.com/gin-gonic/gin@latest #最新的tag 默认不加就是这个选项
```

​    

## go list

用于列出当前项目的`go mod`文件依赖了哪些模块，如果相应模块没有拉去到本地或则安装，则会自动执行`go get`命令进行下载安装，**主要作用就是拿到一个陌生的项目，然后工具该项目的 go.mod 相关依赖信息安装依赖**

```bash
go list -f {{.xxxx}} #指定输出格式
go list -json #以json格式输出项目依赖信息
go list -m all  查看所有依赖以及间接模块当前版本
go list -u -m all 查看所有依赖以及间接依赖当前版本和可升级版本
go list -m -versions github.com/gin-gonic/gin 查看某些模块的所有版本
```

​    

## go mod

`mod`管理的相关命令

```bash
go mod download #下载go.mod声明的依赖库
go mod init github.com/www/my #创建一个go.mod 相当于创建一个项目 my就是项目的名字 开源项目的话一般以github仓库名字命名
go mod tidy #清理go.mod文件中冗余的依赖项
go mod graph #打印模块依赖图
go mod vendor #将依赖复制到vendor下
```

此处专门开辟一篇文章讲`mod`管理

​        

## go run

直接跑代码，而不需要编译生成可执行文件，一般用于程序调试

```bash
go run main.go
```

​    

## go test

执行go测试相关的命令，此处另外开辟一篇文章专门将测试

​    

## 其他命令

```bash
go version #go版本
```

​    

## 其他一些没什么用的命令

### go doc和godoc

`godoc`是一款**文档工具**，Go的文档就是注释，注释就是文档，该工具可以根据注释生成web界面的文档在浏览器上查看，go有个文档仓库，会自动根据你的代码注释生成你github上开源项目的文档 https://pkg.go.dev  所以下面的命令其实也没什么用

```bash
godoc -http :8080 #打开标准库文档
godoc -http :8080 fmt #也可以指定包名
```

`go doc`就是可以直接在控制台打印相关包或则函数、结构体等的注释文档

**但是用这个命令看文档还不如看官网搜集的文档或则直接看代码，所以一般不用，或则使用godoc来在web显示文档**

```bash
go doc
```

### go bug

直接输入`go bug`就会跳转到`github`的`issue`页面，其实也**没什么用**

```bash
go bug
```

### go clean

自动清理一些编译、测试文件，**没什么用**

```bash
go clean
```

### go install

现在基本不怎么用了，用于编译当前项目代码，并且将编译生成的二进制可执行文件放到`$GOPATH/bin`下

```bash
go install
```

