---
title: Go文档管理和规范
date: 2021-02-12
categories: [编程语言/Go]
tags: [Go]
---

## godoc

```bash
godoc -http=:8000 #开启本地文档服务器
```

​    

## Go文档注释规范

因为go的注释即文档，文档都是根据注释生成的，所以文档的详细性和合理性都必须要求注释必须符合一定的规范，这样才可以生成漂亮详细的文档

文档主要有如下几部分组成:

| **组成**  | **作用**                                                     |
| :-------- | :----------------------------------------------------------- |
| Overview  | 包含包的 import 语句和概要说明                               |
| Index     | 包含包中可见性为 public 的常量、类型、方法、函数的总目录及说明 |
| Constants | 项目所有的导出常量                                           |
| Variables | 显示所有全局变量                                             |
| Functions | 显示所有函数                                                 |
| Types     | 显示所有type                                                 |

### Overview

用于整个项目的简单描述，是**项目中一级目录下所有包头开始的注释内容的合并**，一般将这些信息单独写在`doc.go`文件中，比如`gin`的`doc.go`下的内容为：

```go
/*
Package gin implements a HTTP web framework called gin.
See https://gin-gonic.com/ for more information about gin.
*/
package gin // import "github.com/gin-gonic/gin"
```

`doc.go`专门用于存放项目的`Overview`信息，不用于存放代码

### Index

该项目列出了所有导出的结构体、函数、常量等名字以及超链接，用于索引该项目的所有函数、结构体等对象，其详细内容在相应的详细内容上，点击链接就可以看到详细内容

### 具体内容注释

比如有一个函数、结构体、常量、全局变量等，注释在函数声明的上面，并且不能有空行这样才会显示在文档上

```go 
//Say Hello func
func SayHello() {
	log.Println("hello")
}

//Doc常量
const (
	SName = "lyer"
	SAge = 18
)

//var name遍历
/*
dsdad这样也可
*/
var Vname = "dsa"
```

​    

## 文档访问地址

https://pkg.go.dev/<hostname>/<username>/<repo-name>

比如：

- https://pkg.go.dev/go.etcd.io/etcd/clientv3
- https://pkg.go.dev/github.com/biningo/bstree

这些地址需要你手动访问，第一次访问时该库就会去自动拉代码生成文档，每次需要更新文档时再点击相关的跟新文档按钮即可同步最新的文档

​    

## 参考

https://studygolang.com/articles/24533