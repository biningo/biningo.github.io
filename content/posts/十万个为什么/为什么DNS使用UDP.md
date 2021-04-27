---
title: 为什么DNS使用UDP?
date: 2021-04-25
categories: [十万个为什么]
tags: [UDP,TCP,DNS]
---

## DNS协议

在讲解为什么之前我们先来讲解什么是DNS协议，以及DNS协议的一些细节

DNS协议的作用就是建立 **域名和IP地址的映射**，因为IP地址太长并且没有语义所以需要给IP一个域名，DNS协议的作用就是将域名转化为IP地址，这就是DNS协议的主要功能，当然还有其他功能比如记录`CNAME`、IP地址负载均衡等

浏览器在请求一个网址之前需要通过DNS协议获取到这个网址的IP地址这样才可以进行网络通讯建立TCP连接，因为网络中都是以IP地址来识别一个主机的

那么浏览器向谁请求DNS协议呢？这就是DNS服务器的功劳

DNS服务器是专门用来记录域名以及IP地址的映射的一个服务器，其他计算机可以通过DNS协议来请求DNS服务器来查询他所需要的信息，这些信息也是通过DNS协议返回回去的，主机只需要依照DNS协议的规范规则进行请求并且解析响应即可获取信息

DNS服务器主要会记录域名的如下几种记录:

| 类型    | 解释                                                         |
| ------- | ------------------------------------------------------------ |
| `A`     | IP地址记录`Address` 记录域名对应的IP                         |
| `AAAA`  | IPV6的地址记录                                               |
| `NS`    | DNS服务器记录（Name Server），返回记录此服务器的DNS服务器域名 |
| `CNAME` | 规范名称记录（Canonical Name），返回另一个域名，即当前查询的域名是另一个域名的跳转 |
| SRV     | 用于服务发现和负载均衡                                       |

下面来看一个DNS查询`icepan.cloud`的完整过程:

```bash
dig -t A icepan.cloud +trace
```

`.`是根DNS服务器，查询会先根据缓存在本地的根域名服务器的NS记录和A记录去查询到权威域名服务器对应的A记录和NS记录

下面是`13`组根域名服务器对应的NS记录

```bash
.			7117	IN	NS	g.root-servers.net.
.			7117	IN	NS	l.root-servers.net.
.			7117	IN	NS	d.root-servers.net.
.			7117	IN	NS	b.root-servers.net.
.			7117	IN	NS	h.root-servers.net.
.			7117	IN	NS	k.root-servers.net.
.			7117	IN	NS	f.root-servers.net.
.			7117	IN	NS	m.root-servers.net.
.			7117	IN	NS	j.root-servers.net.
.			7117	IN	NS	c.root-servers.net.
.			7117	IN	NS	i.root-servers.net.
.			7117	IN	NS	e.root-servers.net.
.			7117	IN	NS	a.root-servers.net.
```

然后根据这些去根域名服务器中查询顶级域名服务器`.cloud`的NS记录以及对应的IP地址

下面是从根域名服务器中返回的顶级域名服务器`cloud.`的NS记录

```bash
cloud.			172800	IN	NS	b.nic.cloud.
cloud.			172800	IN	NS	d.nic.cloud.
cloud.			172800	IN	NS	c.nic.cloud.
cloud.			172800	IN	NS	a.nic.cloud.
```

最后再从顶级域名服务器中查询权威域名服务器的NS记录和对应的IP

下面是从顶级域名服务器`.cloud`中返回的信息

```bash
icepan.cloud.		3600	IN	NS	dns7.hichina.com.
icepan.cloud.		3600	IN	NS	dns8.hichina.com.
```

最后再从任意一个权威DNS服务器中查询到域名对应的A记录即可

```bash
icepan.cloud.		600	IN	A	121.196.169.248
```

下面来看看DNS协议的报文

![](https://raw.githubusercontent.com/biningo/cdn/master/img/dns.png)

​    

## 为什么DNS协议使用UDP?

首先声明DNS不只是使用了UDP，在DNS数据包大的情况下比如超过了`512byte`则会转化为TCP

所以这个问题可以转化为 **为什么数据量大的时候需要使用TCP？数据量大的时候不能继续使用UDP协议吗？**

这又可以追溯到TCP协议和UDP协议的区别了，如果要传输的数据量很小，比如只是通过DNS协议查询一些IP地址或则CNAME记录，使用TCP协议的话需要进行复杂的3次握手来建立连接传输DNS报文，并且TCP头部信息也是很大的，会造成大头儿子的情况

可见要传输的数据量如果很小那么使用TCP协议传输的话TCP连接以及TCP头部这些消耗都是不可忽视的，但是如果需要传输的数据量很大，那么这些消耗其实可以忽略不计

那么如果数据量大的情况继续使用UDP会有什么问题?

以为底层以太网数据帧规定了一个`MTU`，也就是数据帧的最大的大小，此大小限制了IP数据包的大小，一旦超过了这个大小则需要进行IP分包再组装

如果使用UDP并且数据量很大，虽然UDP会封装为一个报文，但是传输到底层IP层的时候会被分包为多个IP数据包进行传输，IP协议是不负责可靠传输的，这样如果中间的某个IP数据包丢失了那么UDP包就不完整了

如果数据量很小，一个UDP报文不会被进行分包，那么就可以减少丢失的可能性，如果丢了那就再传就行了，反正也就是一个IP包，但是前面的就不一样了，中间的包可能频繁丢失，所以会造成永远也无法完整传输或则全部传输成功的可能性很低

综上，因为现在的网络环境越来越复杂了，DNS协议要做的工作也越来越多，所以如果DNS协议携带的数据很大那么则需要使用TCP协议来保证可靠传输，如果DNS协议携带的数据很小，则可以使用UDP协议来减少消耗

​    

## 参考

[为什么 DNS 使用 UDP 协议](https://draveness.me/whys-the-design-dns-udp-tcp/)