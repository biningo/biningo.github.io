---
title: Docker架构和OCI
date: 2021-06-05
categories: [Docker和容器]
tags: [docker,容器,OCI]
draft: true
---

## 什么是LXC

LXC 全称 `Linux Container` Linux容器

Linux通过`Namespace`和`Cgroup(control group)` 来实现进程之间的隔离，Namespace主要是修改了进程的视图，而Cgroup主要是对进程使用资源的限制，通过Linux的这两个进程特性就可以将一个进程运行在一个沙箱环境中达到完全隔离的效果

Docker的作用并不是虚拟，而是利用了LXC特性来辅助管理、使用、传输容器和镜像

Docker底层就是使用LXC来实现容器的，但是Docker在LXC之上又添加了许多更加便捷的功能，比如: image镜像、Dockerfile打包镜像、镜像仓库、容器运行等功能

Docker最大的创新就是`image`，其底层原理是`UnionFS` ，通过`UnionFS`能有效的节约资源，达到镜像层复用的效果。通过image我们可以很方便的打包环境，传输环境，以此来达到一处构建到处运行的效果

​    

## 什么是OCI

OCI全称 `Open Container Initiative` ，开放容器标准，目前主要有两个标准文

- [runtime spec](https://github.com/opencontainers/runtime-spec)容器运行标准
- [image spec](https://github.com/opencontainers/image-spec)容器打包标准

这个标准是由: RedHat、Google、Docker三大公司共同推进制定的，目的是为了防止Docker在容器领域一家独大，同时也为了统一容器的规范，方便容器生态的统一以及产品的实现

Docker其实就已经是容器的事实标准了，OCI只不过是根据Docker的实现来定制一套标准，有了标准之后各大公司就可以依据这个标准创建自己的容器产品了，只要是符合标准的就可以融入云原生的生态，比如`k8s`等

​    

## Docker架构

![](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/dockerd.png)

docker最初的版本只有一个`dockerd`二进制守护进程和一个`docker`客户端，后来为了架构更好维护、OCI规范的制定等原因被拆分为了多个部分。现在的`dockerd`只保留了一些docker特有的特性，下层都是直接通过调用`containerd`提供的grpc接口来实现和管理容器的

### dockerd

`dockerd` docker daemon，docker守护进程

dockerd的主要功能就是相当于一个后端Restful的HTTP服务器，用于接受处理docker客户端(比如docker命令行、其他客户端、语言API等)的请求

dockerd接收到请求之后就会将其转化为相关的动作，然后通过`grpc`调用下一层`containerd`提供的接口服务

### containerd

符合OCI标准的组件，主要有如下几个功能

- 容器生命周期管理
- 日志管理
- 镜像管理
- 存储管理
- 容器网络接口及网络管理

向上提供`grpc`接口，向下通过调用`runC`API来创建容器

### containerd-shim

TODO

### runC

runC 是 Docker 公司按照 OCI 标准规范编写的一个操作容器的命令行工具，是 libcontainer配上了一个轻型的命令行客户端

主要功能就是创建容器、运行容器

​    

## 参考

[Docker 架构中的几个核心概念](https://www.jianshu.com/p/de3184ad0800)

[Docker (容器) 的原理](https://www.kawabangga.com/posts/4224)

[runC启动容器过程分析](https://imkira.com/runc/)

