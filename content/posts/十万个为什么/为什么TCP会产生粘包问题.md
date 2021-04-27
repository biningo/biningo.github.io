---
title: 为什么TCP会产生粘包问题?
date: 2021-04-25
categories: [十万个为什么]
tags: [TCP]
---

## 概述

**粘包** 就是指应用层的多条消息被合并为一个TCP包了，这个其实在发送端和接收端都会产生

还有一种现象叫**拆包**，指的是应用层数据被拆分为多个TCP包了

​    

## 发送端

**正常的情况**

每个应用层数据都会被封装为一个TCP包然后发送

![](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/tcp-stick.png)

**两个包粘在一起的情况**

如果应用层传递下来的数据太少那么TCP协议会等待更多的数据再发送，这样可以提高传输效率，防止大头儿子的情况

![](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/tcp-stick2.png)

**粘包和拆包一起的情况**

如果应用层数据太大超过了TCP协议的`MSS`大小，则会发生拆包

![](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/tcp-stick3.png)

​    

## 接收端

接收端如果来不及处理数据，那么多个到达的TCP包都会加入缓存队列，此时也会发生粘包现象

​    

## 为什么UDP不会产生粘包问题

因为TCP是面相流的，不认为应用层的消息是一条一条的，只是将应用层传递下来的数据看成一个数据流，没头没尾

而UDP则是基于报文的，将应用层的数据看成是一个完整的数据包，所以不会进行拆分和合并，不管应用层传递下来多大多小的数据都只会封装为一个UDP包进行传输，接收端也一样会将接受到的UDP报文完整的交付给应用层

​    

## 参考

[TCP粘包拆包及解决方法](https://juejin.cn/post/6844904083430572046)

[什么是TCP粘包？怎么解决这个问题](https://www.cnblogs.com/cangqinglang/p/11503057.html)

