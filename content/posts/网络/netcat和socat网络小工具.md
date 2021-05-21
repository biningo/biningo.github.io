---
title: netcat和socat网络小工具
date: 2021-05-19
categories: [网络]
tags: [Linux,网络]
---

## netcat网络瑞士军刀

`netcat`是一个网络小工具，可以用来监听端口，数据回发(简单的echo)，TCP端口扫描，探测网络，网络测速等

echo监听端口和连接端口

```bash
#server -l表示监听
nc -l  8000 #监听8000端口

#client
nc localhost 8000 #连接本地的8000端口

#然后直接输入数据对方就可以收到了

#监听UDP端口
nc -u -l 8080

#请求UDP端口
nc -u localhost 8080
```

传输文件

```bash
#server
nc -l 1234 > filename.out

#client
nc -N localhost 1234 < filename.in #-N表示如果接受到EOF则断开
```

端口扫描和检测端口

```bash
# -z表示只是检测端口 不需要发送数据 
# -v表示显示详细信息
# -w表示指定超时时间s
nc -zv host.example.com 20-30 #[20,30] 
nc -zv icepan.cloud 9000  #端口检测 telnet效果一样
nc -zv -w 5 icepan.cloud 1111 #检测端口设置超时时间为5s
```

结合`pv`网络测速（pv用于测量任何操作的数度，可以以管道符来测量）

```bash
#server
nc -l 8080 < /dev/zero #给这个传输无限多个空白字符串

#client
nc icepan.cloud 8080 > /dev/null | pv #结合pv测速
```

​    

## socat瑞士军刀加强版

`Socket CAT` 其功能与有瑞士军刀之称的 `netcat` 类似，可以看做是 Netcat 的加强版

内网穿透，端口转发

```bash
#外部公网机器
socat tcp-listen:8888 tcp-listen:9999

#内网机器
socat tcp:outerhost:8888 tcp:localhost:7777 #7777是内网目标程序的端口

#之后便可以访问外部服务器的9999端口，就可以访问内网的7777端口
#socat会将9999端口的流量转发到8888端口，内网机器就可以讲8888端口的数据转发到7777端口
```

端口监听

```bash
socat tcp-listen:12345 -

#往端口发数据
socat - TCP:localhost:8000 
echo "test" | socat - tcp-connect:127.0.0.1:12345
```

​    

## 参考

[Socat 入门教程](https://www.hi-linux.com/posts/61543.html)