---
title: WebSocket协议
date: 2021-03-16
categories: [网络]
tags: [网络协议]
---

##  WebSocket要解决的问题

由于HTTP是请求应答模式，所以如果是聊天室这样的项目的话，那么为了立即获取到信息浏览器就必须进行不断的轮询服务器，不仅效率低下，而且还无法立即获取到数据

于是就有了`WebSocket`协议，`WebSocket`是通过HTTP协议进行握手然后再进行通讯的，因为浏览器无法同服务器直接建立TCP连接，所以只能先通过HTTP协议建立一个TCP连接通道，之后再升级协议采用WebSocket协议，这样WebSocket就和HTTP采用相同的端口进行和服务器通讯了

```bash
ws://www.chrono.com
ws://www.chrono.com:8080/srv
wss://www.chrono.com:445/im?user_id=xxx #加密的websocket协议
```

​    

## WebSocket握手

`WebSocket`握手是通过HTTP协议进行的

首先浏览器请求升级协议

```http
GET /xx HTTP/1.1
Connection: Upgrade
Upgrade: websocket
Sec-WebSocket-Key: sdadsxxada==
Sec-WebSocket-Version: 13
```

`Sec-WebSocket-Key` 是一个 Base64的16byte的随机数 用于简单认证，防止误连

然后浏览器返回`101`响应

```http
HTTP/1.1 101 Switching Protocols
Sec-WebSocket-Accept: dsdada
```

`Sec-WebSocket-Accept` 是把请求头里`Sec-WebSocket-Key`的值，加上一个专用的 UUID “258EAFA5-E914-47DA-95CA-C5AB0DC85B11”，再计算 SHA-1 摘要，客户端接受到响应之后使用相同的方法计算出摘要然后判断是否相等，相等表示认证成功

握手成功之后传递的就是WebSocket报文了

​    

## WebSocket报文格式

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/websocket.png)

| 域             | 说明                                                         |
| :------------- | :----------------------------------------------------------- |
| FIN            | 1bit，是否为信息的最后一帧                                   |
| RSV 1-3        | 1bit，备用，默认为0                                          |
| opcode         | 4bit，帧类型 `1` 表示帧内容是纯文本，`2` 表示帧内容是二进制数据，`8` 是关闭连接，`9` 和 `10 `分别是连接保活的 PING 和 PONG |
| MASK           | 1bit 掩码，是否用`XOR`进行简单加密数据。 客户端发送给服务端时，mask必须为1，否则断开连接。 服务端发送给客户端时，mask必须为0，否则断开连接。 |
| payload length | 表示帧的长度。它是另一种变长编码，最少 `7` 位，最多是 `7+64` 位，一个 WebSocket 帧最大是 `2^64` |
| Masking-key    | 0或32 bit掩码值(Mask为1时才有)，是一个`4byte`的随机数        |
| Playload data  | 长度为Payload len的数据，如果有掩码，需要用Masking-Key来异或解密 |

​    

## 参考

https://www.ruanyifeng.com/blog/2017/05/websocket.html