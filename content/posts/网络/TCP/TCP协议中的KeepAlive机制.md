---
title: TCP协议中的KeepAlive机制
date: 2021-05-27
categories: [网络/TCP]
tags: [TCP,网络]
---

## HTTP中的keep-alive

在HTTP的头部中，也有一个字段

```http
connection: keep-alive
```

此字段告诉服务器HTTP会复用这一条TCP连接，当前的HTTP请求完毕之后不会立即断开连接，还可以被另外的HTTP请求复用这条连接，这样就可以减少建立TCP连接的次数

​    

## TCP的长连接和短连接

TCP连接并没有分为长短连接，长短连接只是在于你如何使用TCP

如果建立TCP连接短暂通讯结束之后立即释放这条连接则此连接就为短连接，比如我HTTP发一次请求就建立一次TCP连接，HTTP响应回来之后就立即断开TCP连接

短连接的缺点就是需要频繁的建立TCP连接，比较耗时，优点就是可以很快的释放TCP连接队列，以便于空出位置建立其他TCP连接

如果建立TCP连接在使用时候不立即断开，如果一直没有请求的话就将这条连接空闲即可，以便与后面如果还要请求通讯的话就不需要建立TCP连接了，直接复用那条已经建立好的TCP连接即可。比如我HTTP请求建立TCP连接，响应结束之后浏览器不会立即释放与服务器的TCP连接，如果下次还有其他请求的话则可以复用之前建立的TCP连接即可，如果一时半会儿还没有请求的话只需要将TCP连接空闲着即可，等下次有请求了就可以继续用这条连接进行传输

长连接的优点就是可以减少建立TCP连接的消耗，复用连接。缺点就是如果很长一段时间都没有数据可发的话就会占用操作系统的TCP全连接队列，如果TCP全连接队列比较紧张的话那么就无法建立其他的TCP连接了

​        

## TCP的KeepAlive机制

TCP的KeepAlive机制会在指定的间隔时间发送`KeepAlive`探活包，如果收到了`KeepAlive`包的ACK则表明对方还在，如果多次发送`KeepAlive`包都得不到回复则可以认为对方已经挂了，就可以将这个TCP连接从队列里面删除了

`KeepAlice`主要有如下几个作用:

- **保活**
- **解决窗口值为0的情况**

在空闲的长连接里我们可以通过`KeepAlive`机制探测对方是否存活，如果收到ACK则表明对方还存活并且还需要复用这条连接

如果多次发送`KeepAlive`都收不到则说明对方已经挂了或则对方拒绝回复，这样就表明这条长连接可以断开清除了。如果没有KeepAlive机制如果对方挂了则这个连接还会一直保留在服务区占用着TCP全连接队列

还有一种情况就是在接收方的窗口值为0的情况下，那么发送方就不能继续发送数据了，那么即使接收方的窗口扩大了也没有办法告诉发送方，此时就相当于死锁了双方都不会发送数据

所以KeepAlive探测包还可以用于解决窗口值减少为0而造成死锁的情况。当接收方的窗口为0的时候，发送方可以定时的发送KeepAlive包来探测接收方的窗口是否扩大了

​    

## Linux中KeepAlive相关配置

```bash
#7200 每7200s发送KeepAlive包
/proc/sys/net/ipv4/tcp_keepalive_time

#75 如果75s内没有收到KeepAlive的ACK则再次发送
/proc/sys/net/ipv4/tcp_keepalive_intvl 

#9 如果重试9次都没有响应则把此连接删除掉
/proc/sys/net/ipv4/tcp_keepalive_probes
```

​    

## KeepAlive能携带数据吗

注意，KeepAlive包的seq也是没有意义的，seq一般和初始化的seq一致，KeepAlive是不携带数据的