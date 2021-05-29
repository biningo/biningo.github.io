---
title: Linux网络工具和命令
date: 2021-05-28
categories: [网络]
tags: [Linux,网络]
draft: true
---

## 网络配置

`ifconfig`

```bash
ifconfig #查看所有网卡信息
ifconfig lo #查看指定网卡信息
ifconfig wlp0s20f3 up/down #开启/关闭网卡
ifconfig wlp0s20f3 192.168.0.24 #为网卡分配指定IP
```

`ip`

​    

## 连通性探测

`ping`

```bash
ping github.com -c 2 #-c 发送2个ping即结束
ping github.com -w 3 #-w ping的时间s
```

`telnet`

```bash
telnet 192.168.0.23 8080 #探测端口是否连通
```

`mtr`

```bash
-n 直接显示IP 不解析域名
-s 10 ping数据包大小 byte
-c 20 发送数据包的数量

mtr github.com #动态的打印在终端
mtr -rn -s 10 -c 20 github.com #生成报告
```

- 第一列是IP地址
- 丢包率：`Loss`
- 已发送的包数：`Snt`
- 最后一个包的延时：`Last`
- 平均延时：`Avg`
- 最低延时：`Best`
- 最差延时：`Wrst`
- 方差（稳定性）：`StDev`

参考: 

- [MTR 命令使用详解](https://wker.com/mtr/)
- [Diagnosing Network Issues with MTR](https://www.linode.com/docs/guides/diagnosing-network-issues-with-mtr/)

​    

## 网络连接状态

`netstat`

```bash
-n: 表示只显示IP地址
-u: UDP
-x: UNIX本地socket
-4 查看ipv4
-6 查看ipv6

netstat -i #查看网络结构的信息
netstat -s #查看网络协议栈相关的统计信息
netstat -nr #查看本地路由表的信息
netstat -at #列出所有TCP端口
netstat -ntl #列出处于LISTEN状态的TCP
netstat -ntlp #-p表示列出端口对应的进程是哪个
```

`ss ` 

Socket Statistics 作用和netstat一样，只是速度会快点

```bash
ss -ntl4
```

`lsof`

lists openfiles  列出被打开的文件

​    

## 网络路由

`arp` 

```bash
#查看本机的ARP缓存
arp -n 
```

`route`

查看和修改路由表，同样工具还有 netstat -r 和 ip route

```bash
route -n #查看本机路由表
```

​    

## DNS域名相关

`host` 

```bash
host github.com #查看域名相关信息比如IP等
```

`nslookup`

```bash
nslookup icepan.cloud #查看域名对应的IP
```

`dig`

```bash
dig github.com
```

​    

## 防火墙

`iptables`

​    

## 抓包工具

`tcpdump`

​    

## 网络工具

- netcat(nc)

- socat

## 参考

[这是一份你需要的Linux 网络命令大全](https://juejin.cn/post/6844903844267180039)