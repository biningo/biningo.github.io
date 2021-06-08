---
title: Docker为什么不安全
date: 2021-06-05
categories: [Docker和容器]
tags: [docker,容器,网络安全]
draft: true
---

## 概述

Docker属于进程隔离，所以安全机制肯定不如虚拟机。如果虚拟机被黑客攻击了那么破坏的也只是虚拟机，但是如果Docker容器被黑客攻击了，那么直接影响的就是宿主机，如果宿主机被攻击了将会影响宿主机上的所有进程

​    

## volumes挂载安全

`volumes`挂载的时候如果挂载了一个宿主机中的比较重要的文件，那么如果黑客通过容器修改了挂载的文件那么就会影响宿主机

下面演示一个不安全的例子

- 用户`testuser`加入到`docker`组里面，则用户`testuser`可以执行docker命令启动容器
- 但是`testuser`没有sudo权限

下面登入`testuser`运行一个`busybox`并且绑定一个`volumes`

```bash
docker run -it --name busybox -v /etc/sudoers:/sudoers  busybox:1.32.0 sh
```

testuser是一个没有`sudo`权限但是可以执行`docker`命令的一个用户，由于上面绑定了一个`volumes`，于是testuser就可以通过修改容器里面的`/sudoers`来达到修改宿主机中的`/etc/sudoers`文件的目的

只需要往文件里面加上下面一句，testuser就可以有sudo权限了

```bash
testuser ALL=(ALL) NOPASSWD:ALL
```

​    

## 为什么docker会不安全

归根结底就是docker的后台进程需要root权限运行，包括`docker.sock`

​    

## 参考

[Docker vs Podman | 两者的区别是什么？](https://www.bilibili.com/video/BV1YU4y1p7jG?from=search&seid=2671644522120768397)