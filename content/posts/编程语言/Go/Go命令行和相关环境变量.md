---
title: Go命令行和相关环境变量
date: 2021-02-12
categories: [编程语言/Go]
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

拉取依赖并且编译安装代码的命令

​    

## go list

列出`go.mod`依赖了哪些库

​        

## go mod

`mod`管理的相关命令

​        

## go test

执行go测试相关的命令

​    

## go run

直接跑代码，而不需要编译生成可执行文件，一般用于程序调试

```bash
go run main.go
```

​      

## 其他命令

```bash
go version #go版本
```

​    

## 没什么用的命令

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

