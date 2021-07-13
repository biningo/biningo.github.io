---
title: Kratos源码分析及其周边生态
date: 2021-07-13
categories: [创高实习]
draft: true
---

## kratos源码概述

每个文件都会有接口限定声明，不仅能在编译期间就检查出是否完全继承了接口，而且让代码更容易阅读，一看就知道这个struct实现了哪些interface，而不需要借助IDE查看其实现关系 (因为go接口实现是隐式实现，带来了灵活性同时牺牲了代码可读性，不借助IDE更本无法判断这个struct实现了哪些接口)

```go
var _ config.Source = (*file)(nil)
```

config的file实现使用`github.com/fsnotify/fsnotify`库实现配置变动监听，`viper`库底层也是使用了此库，是文件变动监听的事实标准

​     

## config配置模块

config的New方法通过`Option`的方式添加配置，由用户选择传入哪些配置，只需要传入`WithXXX`即可设置options结构体属性，框架方面只需要提供相关的`WithXXX`函数即可，这个options相当于一个配置结构体

这样的好处就是用户可以动态的选择需要设置哪些配置，不用硬编码直接写死在构造函数上，通过将所有属性抽象一个`Option`回调函数再结合go函数的可变参数特性，用户传入定义好的回调函数，创建时再调用这些函数设置相关的配置属性

外部用户需要设置哪些参数只需要传入指定的With函数即可，下面是我模拟这种风格的案例代码

> 很多库都使用这种Option方式进行配置结构体属性，方便用户选择以及扩展
>
> 对修改关闭对扩展开放

```go
package main

import "log"

/**
*@Author lyer
*@Date 7/13/21 10:16
*@Describe
**/

type Student interface {
	Hello()
}

type options struct {
	address string
	hobbies []string
}

type student struct {
	ID   int
	name string
	opts options
}

//Hello 此函数就可以使用options里面的相关配置来实现功能了
func (s student) Hello() {
	log.Println("Hello,I'm", s.name, "at", s.opts.address)
	for _, hobby := range s.opts.hobbies {
		log.Println("I like", hobby)
	}
}

type StudentOption func(*student)

func NewStu(id int, name string, opts ...StudentOption) Student {
	stu := student{
		ID:   id,
		name: name,
	}
	for _, opt := range opts {
		opt(&stu)
	}
	return stu
}

func WithStuAddress(address string) StudentOption {
	return func(stu *student) {
		stu.opts.address = address
	}
}

func WithStuHobbies(hobbies ...string) StudentOption {
	return func(stu *student) {
		stu.opts.hobbies = hobbies
	}
}

func main() {
	stu := NewStu(
		1000,
		"icepan",
		WithStuAddress("杭州"),
		WithStuHobbies("running", "basketball"),
	)
	log.Println(stu)
	stu.Hello()
}
```

Kratos只实现了file文件类型的Source，我们如果是使用注册中心网络传输配置的话则只需要自己去实现`Source`接口即可，此接口只有两个方法

```go
type Source interface {
	Load() ([]*KeyValue, error)
	Watch() (Watcher, error)
}
```

​     

## 注册中心和配置中心

注册中心需要实现kratos的相关接口即可，然后在kratos的app中run起来之后就会调用注册方法进行注册

kratos官方为我们实现的consul注册中心`https://github.com/go-kratos/consul` 

kratos实现的etcd注册中心还在看代码中



## wire依赖注入

> 编译时依赖注入 (Spring是运行时注入)

参考博客:

- https://darjun.github.io/2020/03/02/godailylib/wire/

