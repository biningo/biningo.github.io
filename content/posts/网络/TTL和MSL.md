---
title: TTL和MSL
date: 2021-05-14
categories: [网络]
tags: [TCP,IP]
---

## TTL

`Time To Live` 指定了IP包允许跳转(允许通过的最大网段数量)的路由器数量，最大值为`255`，推荐值为`64`

TTL在IP数据包中表示

![](https://raw.githubusercontent.com/biningo/cdn/master/2021-04/ip_package.png)

IP数据包每经过一个路由器其值就会-1，一旦`TTL=0`路由器就会将该IP数据包丢弃，并向IP包的发送者发送 `ICMP time exceeded`消息

TTL主要就是为了**防止IP数据包在网络上出现无限的循环跳转**，一旦出现循环跳转的话就会比较浪费网络资源

​    

## MSL

 `Maximum Segment Lifetime`最大报文段生存时间，此时间应该是略大于`TTL`跳转的时间的，也就是说一旦IP包只要送到对方的机器上，那么此包就一定不会超过`MSL`，想象一下如果MSL小于TTL那么IP包送到了但是TCP包超时了那么就会比较浪费资源，并且因为TCP包是包含在IP包上的，里面的时间按逻辑上就应该大于等于TTL的

**所以只要经过了MSL时间，那么TCP包就肯定已经被传输线路上的路由器给丢弃了**

​    

## 参考

[TCP/IP](https://www.processon.com/outline/view/5ea695047d9c0869daaa6e79)

