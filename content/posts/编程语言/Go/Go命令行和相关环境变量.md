---
title: Go命令行和相关环境变量
date: 2021-02-12
categories: [编程语言/Go]
tags: [Go]
---

## go命令

### go build

将代码编译为相关平台的可执行文件，只需要编译带有`main`的入口文件即可

```bash
go build #会寻找当前目录下main入口文件然后进行编译 会编译所有
go build -o main  #指定生成可执行文件的名字
go build mymain.go #也可以编译指定的go文件 然后就会连一起依赖的代码都编译为一个二进制
```

### go env

用于管理go的环境变量相关信息，go相关环境变量也可在`.bashrc`等文件里面设置，优先级高

```bash
go env #打印所有go的环境变量
go env GOPROXY #打印某个环境变量的值
go env -json #json格式输出
go env -w GOPROXY=https://goproxy.cn,direct #修改某个值 这里设置了中国代理，direct表示如果代理没有则直接走go官网，可以设置多个代理网站，用逗号分割
```

### go fmt和gofmt

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

### go get

拉取依赖并且编译安装代码的命令

### go list

列出`go.mod`依赖了哪些库

### go mod

`mod`管理的相关命令

### go test

执行go测试相关的命令

### go run

直接跑代码，而不需要编译生成可执行文件，一般用于程序调试

```bash
go run main.go
```

### go vet

静态代码检查命令，涉及代码逻辑的检查

```bash
go vet main.go #对单个文件
go vet ./... #对整个项目进行检查
```

### go tool

用于执行一些go的工具命令，比如：

```bash
go tool vet #和go vet不一样
go tool doc #和go doc 不一样
go tool fix #......
.....
```

​    

### go doc

`godoc`是一款**文档工具**，需要外部安装，Go的文档就是注释，注释就是文档，该工具可以根据注释生成web界面的文档在浏览器上查看，go有个文档仓库，会自动根据你的代码注释生成你github上开源项目的文档 https://pkg.go.dev ，前提是第一次的话需要手动加入该库(执行go get或则到上面的网站上去搜索并且添加) 

```bash
godoc -http :8080 #godoc会为$GOROOT和根据当前项目下的mod文件建立文档
```

**这就是可以通过本地来查看文档了，一般用处不大，都是到官网上去看的**

在go官网上，则直接访问 `https://pkg.go.dev/github.com/<username>/foo` 这样就访问到了`username`的`foo`仓库的代码文档

`go doc`就是可以直接在控制台打印相关包或则函数、结构体等的注释文档

**但是用这个命令看文档还不如看官网搜集的文档或则直接看代码，所以一般不用，或则使用godoc来在web显示文档**

```bash
go doc
```

​        

### 其他命令

```bash
go version #go版本
go fix #把指定代码包的所有Go语言源码文件中的旧版本代码修正为新版本的代码，这里的版本是指Go语言本身的版本
```

### 没什么用的命令

go bug

直接输入`go bug`就会跳转到`github`的`issue`页面，其实也**没什么用**

```bash
go bug
```

go clean

自动清理一些编译、测试文件，**没什么用**

```bash
go clean
```

go install

现在基本不怎么用了，用于编译当前项目代码，并且将编译生成的二进制可执行文件放到`$GOPATH/bin`下

```bash
go install
```

​    

​    

## go相关环境变量[TODO]

```bash
GO111MODULE="on" #开启Go Modules 默认开启
GOARCH="amd64" #CPU架构 如:arm amd64等
GOBIN="/home/lyer/data/go-path/bin" #编译产生的可执行二进制文件的安装目录  默认$GOPATH/bin
GOCACHE="/home/lyer/.cache/go-build" 
GOENV="/home/lyer/.config/go/env" #go环境变量配置文件 也可以配置到系统环境变量上
GOEXE=""
GOFLAGS=""
GOHOSTARCH="amd64"
GOHOSTOS="linux" 
GOINSECURE=""
GOMODCACHE="/home/pb/data/go-path/pkg/mod"  #go mod代码下载地址
GONOPROXY="*.gitlab.com"
GONOSUMDB="*.gitlab.com"
GOOS="linux" #操作系统平台 win linux darwin(mac)
GOPATH="/home/pb/data/go-path" #GOPATH
GOPRIVATE="*.gitlab.com"
GOPROXY="https://goproxy.cn,direct" #go get下载代理地址
GOROOT="/home/pb/program/go" #go安装目录
GOSUMDB="off"
GOTMPDIR=""
GOTOOLDIR="/home/pb/program/go/pkg/tool/linux_amd64"
GCCGO="gccgo"
AR="ar"
CC="gcc"
CXX="g++"
CGO_ENABLED="1"
GOMOD="/dev/null"
CGO_CFLAGS="-g -O2"
CGO_CPPFLAGS=""
CGO_CXXFLAGS="-g -O2"
CGO_FFLAGS="-g -O2"
CGO_LDFLAGS="-g -O2"
PKG_CONFIG="pkg-config"
GOGCCFLAGS="-fPIC -m64 -pthread -fmessage-length=0 -fdebug-prefix-map=/tmp/go-build029755246=/tmp/go-build -gno-record-gcc-switches"
```

`GOOS`和`GOARCH`的值成对出现，而且只能是下面列表对应的值。

```bash
$GOOS	    $GOARCH
android	    arm
darwin	    386
darwin	    amd64
darwin	    arm
darwin	    arm64
dragonfly   amd64
freebsd	    386
freebsd	    amd64
freebsd	    arm
linux	    386
linux	    amd64
linux	    arm
linux	    arm64
linux	    ppc64
linux	    ppc64le
linux	    mips
linux	    mipsle
linux	    mips64
linux	    mips64le
linux	    s390x
netbsd	    386
netbsd	    amd64
netbsd	    arm
openbsd	    386
openbsd	    amd64
openbsd	    arm
plan9	    386
plan9	    amd64
solaris	    amd64
windows	    386
windows	    amd64
```

编译在64位Linux操作系统上运行的目标程序

```bash
$ GOOS=linux GOARCH=amd64 go build main.go
```

编译arm架构Android操作上的目标程序

```bash
$ GOOS=android GOARCH=arm GOARM=7 go build main.go
```