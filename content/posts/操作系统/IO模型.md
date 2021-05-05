---
title: IO模型
date: 2021-05-02
categories: [操作系统]
tags: [IO,Linux]
draft: true
---

## 直接IO和非直接IO

磁盘 I/O 是非常慢的，所以 Linux 内核为了减少磁盘 I/O 次数，在系统调用后，会把用户数据拷贝到内核中缓存起来，这个内核缓存空间也就是**页缓存**，只有当缓存满足某些条件的时候，才发起磁盘 I/O 的请求

- **直接 I/O**: 不会发生内核缓存和用户程序之间数据复制，而是直接经过文件系统访问磁盘
- **非直接 I/O**: 读操作时，数据从内核缓存中拷贝给用户程序，写操作时，数据从用户程序拷贝给内核缓存，再由内核决定什么时候写入数据到磁盘

内核会在一下几种情况下对真实的磁盘进行读写:

- 内核缓存区满了
- 用户主动调用`sync`
- 缓存数据超过指定时间
- ....

​    

## 缓冲IO和非缓冲IO

**缓冲IO** 指的是标准库在应用程序进行读写数据的时候会开辟一个缓冲区，进行系统调用的时候回**先调用标准库函数，然后标准库函数再去进行系统调用读取数据**，系统调用会预先读取一部分数据缓存在用户标准库里面的缓冲区中，这样就不必每次读取数据都进行系统调用了，减少了系统调用的开销

**非缓冲IO** 则是每次读写数据都直接进行系统调用，系统调用结果直接返回到应用程序

​    

## 阻塞IO(BIO)和非阻塞IO(NIO)

**阻塞IO** 指的是用户线程在`read`系统调用时会一直等待内核读取数据，等内核读取数据到内核缓冲区(**比如从网卡读取数据、通过底层文件系统从磁盘读取数据到内核缓冲区**)然后将内核缓冲区数据copy到用户空间，这样整个`read`调用才会返回

阻塞IO线程需要等待的时间: **操作系统读取数据时间+内核缓冲区到用户缓存区copy的时间**

[![img](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/BIO.png)](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/BIO.png)

**非阻塞IO** 指的是用户线程在`read`系统调用之后可以立即返回，但是需要自己主动再次向内核询问数据是否读取到内核缓冲区中，这里需要多次询问直到数据读取完毕(在轮询期间可以干一些其它事情)，当数据准备好了之后，则需要阻塞的等待内核将内核缓冲区的数据copy到用户空间中(**注意这个过程是阻塞的**)

非阻塞IO线程需要等待的时间: **不断轮询的消耗的时间+内核缓冲区到用户缓存区copy的时间**

[![img](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/NIO.png)](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/NIO.png)

​    

## IO多路复用

传统的非阻塞IO模型有如下缺点:

- **一个线程不断轮询效率低下问题**
- **一个线程只能监听一个IO，为了达到多个网络IO并发连接就只能多线程，多线程多进程有系统调度的开销**

于是就出现了 **IO多路复用**

多路复用是一种 **非阻塞IO模型** ，传统同步IO模型比如阻塞IO和非阻塞IO只能在一个线程中监听一个IO句柄，并且需要用户程序主动轮询，但是多路复用则可以**在一个线程中监听多个IO句柄，并且不需要用户程序主动轮询而，是由操作系统帮你监听，如果有数据可读则通过系统调用直接返回，相当于进行系统调用操作系统帮你进行轮询管理多个IO句柄了**，所以非阻塞IO需要操作系统的支持

数据从内核缓冲区copy到用户缓冲区等一系列操作，这个copy的过程还是阻塞的，因此多路复用是一种 **同步IO模型中的非阻塞IO**

[![img](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/MIO.png)](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/MIO.png)

​    

## IO多路复用的系统调用函数

### select

```c
int select(
    int nfds, //最大fd+1
    //fd_set是个数组 最大监听1024个fd
  	fd_set *readfds, //读事件 比如socket fd可读了
    fd_set *writefds, //写事件 比如socket缓冲区可写了
    fd_set *writefds, //异常事件
    struct timeval *timeout //超时时间 为NULL则表示如果没有fd可读则阻塞等待
);
```

- 监听的fd数量有限
- 每次需要将fd在用户空间到内核空间来回copy
- 产生惊群，只要有一个fd可读，那么用户进程必须遍历所有fd去检查到底是哪个fd可读

### poll

因为poll将fd集合用**链表**来保存，所以poll能监听的fd数量没有限制（小于操作系统的最大打开文件描述符个数），其他都和select区别不大

### epoll

```c
//初始化一个epoll句柄 返回一个句柄
//epoll_create1(EPOLL_CLOEXEC);
int epoll_create1 (int flags);

//epoll注册等操作
int epoll_ctl(
	int epfd, //create出来的epoll句柄
    int op, //操作类型 增加,删除..
    int fd, //待操作的fd
    struct epoll_event *events //epoll事件
)

    
int epoll_wait(
	int epfd,
    struct epoll_event *events,
    int maxevents //最大的事件数量
    int timeout
)
```

​        

## 异步IO(AIO)和同步IO

[![img](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/io.png)](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/io.png)

同步IO中用户程序还是会在内核缓冲区copy到用户缓存区的时候阻塞等待，而异步IO在 **内核数据准备+数据从内核态拷贝到用户态**这两个过程都不用等待，发起`aio read`就立即返回，用户不需要等待也不需要轮询，操作系统会在后台进行数据准备，数据准备完毕之后会将数据从内核态copy到用户数据区域，这一系列动作都完成之后就会通知用户程序，用户程序收到通知之后数据就已经在自己的用户空间了

[![img](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/AIO.png)](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/AIO.png)

​    

## 参考

[一口气搞懂「文件系统」，就靠这 25 张图了](https://mp.weixin.qq.com/s/qJdoXTv_XS_4ts9YuzMNIw)

[彻底理解 IO多路复用](https://juejin.cn/post/6844904200141438984)

[协程和IO多路复用更配哦~](https://www.bilibili.com/video/BV1a5411b7aZ?from=search&seid=16788754659546307329)