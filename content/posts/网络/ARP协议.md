---
title: ARP协议
date: 2021-03-03
categories: [网络]
tags: [网络协议]
draft: true
---

## 什么是ARP协议

**ARP协议** 是一种位于 **网络层** 和 **IP协议** 同级的协议，用于 **根据IP获取MAC地址** 

由于最终数据要到达目标主机，以太网/局域网中必须通过`MAC`地址来唯一识别一台主机，所以在链路层发送数据包必须知道目标主机的MAC地址以此封装一个个链路层的以太网格式的数据帧来传递，ARP在发送请求的时候也会被封装为以太网格式的数据链路帧，目的MAC地址为局域网的广播地址

也有`RARP`协议，根据MAC获取IP，用的不多

​    

## ARP协议过程

- 源主机要发送数据时会先检查本机的 **ARP映射缓存**(记录了IP地址和MAC地址的映射关系,方便快速查找,该缓存有一个失效时间,因为IP地址和目标主机的映射可能会变所以必须设置失效时间)

- 如果本地缓存有则直接获取到目标主机的MAC地址
- 如果本地缓存没有则需要广播发送一个局域网内的ARP请求包，所有的局域网内的主机都会收到，如果目标IP是自己则响应这个ARP请求，将自己的MAC地址响应回去，如果不是则丢弃ARP包 (**这里会有一个ARP欺骗问题**)
- 收到到ARP响应之后就获取了目标机器的MAC地址，则本机就会将映射关系缓存起来然后就可以将IP数据报封装为以太网中的 **MAC数据帧(包含源MAC地址和目的MAC地址的数据帧)**  然后再发送数据包给目标机器了

​    

## ARP数据包结构

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/arp.png)

- **op操作类型**: `1`表示是ARP请求，`2`表示是ARP响应

下面请看WireShark的抓包

ARP请求:

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/arp2.png)

ARP响应

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/arp3.png)

另外还需要注意在以太网MAC帧的`Type`字段需要设置为`0x0806`标识数据报是ARP数据报，同时MAC帧的目的的MAC地址是`ff:ff:ff:ff:ff:ff`，这是一个MAC广播地址

​    

## 相关命令

`arp`命令

```bash
arp -a #获取本机的ARP缓存
cat /proc/net/arp #也可以获取本机ARP缓存
```

查看本机MAC地址的方法

```bash
cat /sys/class/net/eth0/address
ifconfig
```

​    

