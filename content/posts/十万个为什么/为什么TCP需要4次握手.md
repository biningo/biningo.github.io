---
title: 为什么TCP需要4次握手
date: 2021-04-25
categories: [十万个为什么]
tags: [TCP]
---

## 为什么TCP需要4次握手

<img src="https://raw.githubusercontent.com/biningo/cdn/master/img1/tcp-bye.png"  />

第一次: A没数据发送了则主动要求断开连接，于是A向B发送了一个`FIN`包

第二次: B收到A的`FIN`包则需要回复一个ACK表示收到了A的`FIN`请求，但是此时B可能还有数据需要发给A(比如之前没有收到A的ACK的数据需要重发等)，这种情况下A可以继续接受B的数据但是不会主动发送数据给B只会回复ACK，所以B发送的ack号永远都是一样的

第三次: B将剩余的数据发送完毕之后就需要发送一个`FIN`包给A表示”自己的数据发送完毕了，我要断开了“

第四次: 此时A必须回复这个`FIN`表示收到了B的断开请求，此时就进入 **TIME_WAIT** 状态等待一段时间才会断开，B如果收到了A的ACK则断开连接，如果没收到则还会继续发送`FIN`请求，此刻因为A还处于 **TIME_WAIT** 状态则会继续回复ACK

​    

## 为什么需要 TIME_WAIT

- 回复B最后一个`FIN`包
- 重发ACK包
- 接受之前还遗留在网络上的数据包

​    

## 参考

[https://www.bilibili.com/video/BV1x441177hF?from=search&seid=3231512348930851320](https://www.bilibili.com/video/BV1x441177hF?from=search&seid=3231512348930851320)