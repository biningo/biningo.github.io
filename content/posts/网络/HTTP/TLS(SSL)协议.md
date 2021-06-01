---
title: TLS(SSL)协议
date: 2021-03-16
categories: [网络]
tags: [TLS/SSL,网络安全,HTTPS]
---

## 什么是TLS协议

因为HTTP传输都是明文传输的，如果被中间人截获，那么报文内容就会被盗取

为了防止HTTP明文传输，于是可以讲HTTP报文和数据整个进行加密然后再将数据传递给下层的TCP层，传递到客户端再进行解密，`TLS`协议就是解决HTTP报文加密问题的，被TLS协议加密之后的HTTP协议就叫`HTTPS` **HTTP over SSL** 使用的是`443`端口

> TLS是SSL的改进版，现在都是使用TLS来替换SSL了，但是延续了老的叫法，所有都叫`SSL` 这两个其实是同一个意思
>
> TLS现在广泛使用的版本为`TLS1.2` `TLS1.3`

![在这里插入图片描述](https://user-gold-cdn.xitu.io/2020/4/29/171c19d411a7eacb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

加密算法有**对称加密、非对称加密** ，如果将HTTP报文采用非对称加密的话那么带来的CPU运算是非常大的，速度也相对较慢，因为HTTP报文一般数据比较大

所以TLS采用对称加密的方式来加密HTTP报文加快加密解密的速度，但是这里就涉及到如何传递 **对称密钥** 的问题了，这就是 **TLS握手** 需要解决的问题

TLS握手的作用:

- 商定双方使用的非对称加密算法和TLS版本
- 传输非对称加密算法所需的参数和随机数，随机数的作用是加大解密的难度
- 传输证书和公钥 (非对称加密算法需要使用公私钥)

​     

## TLS握手第一阶段

### 1、Client Hello

TCP建立之后浏览器会先发送一个`Client Hello` 报文给服务器，目的是为了告诉服务器如下内容，重点关注如下内容:

- 客户端TLS版本
- 支持的加密套件`Cipher Suites`  服务器会从中选择一个加密算法
- 随机字符串`Random1` ， 这个随机数和之后的生成对称密钥有关
- Session ID 用来**恢复会话** 
- SessionTicket 客户端保存的TLS的session信息

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/ClientHello.png)

### 2、Server Hello

服务器接受到客户端的`Client Hello`之后就会检查客户端版本、加密套件等信息，检查通过并且符合服务器的条件则服务器会进行回复，也就是发送`Server Hello`包

回复内容主要是告诉客户端如下几个重要的内容:

- 确认`SSL/TLS`协议版本，如果浏览器不⽀持，则关闭加密通信

- 从`Client Hello`中选择一个加密套件，如选择`RSA`，并且告诉客户端选择了那个加密套件用于后面的加密
- 随机字符串`Random2`，此时客户端和服务器都拥有两个随机数 `Random1、Random2`
- 初次握手服务器会返回一个Session ID，客户端会保存这个ID，之后再进行握手发送Client Hello的时候就会携带上这个Session ID，服务器就会直接查询Session ID对应的信息比如对称加密的密钥，这样就可以直接复用了就不需要再次生成了，因为生成过程也有一定的消耗

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/ServerHello.png)

​    

## TLS握手第二阶段

第二阶段，双方已经确定了:

- 双方协商的非对称加密的算法 (比如RSA)
- `Random1`
- `Random2`

这一阶段中，全部都是服务器在发送消息，服务器会连发还几个包

### 1、Certificate

服务器的证书，客户端收到之后需要进行验证证书是否有效，然后就可以拿到服务器证书里面的公钥了，公钥用于加密第3个随机数。有了证书的证明，则此公钥就一定是服务器的

### 2、Server Key Exchange

服务器传递计算密钥算法的一些参数，这样他们才可以根据相同的参数和算法计算出相同的 **对称密钥**

### 3、Server Hello Done

表示服务器的信息发送完毕

​    

## TLS握手的第三个阶段

### 1、Client Key exchange

客户端收到算法计算公式的参数之后再次生成一个随机数`Pre-Master`，此随机数就必须要用服务器的公钥加密之后发给服务器，服务器收到到`Pre-Master`之后即可以 结合 `Random1+Random2+Pre-Master` 和加密算法的公式再次计算出 `Main-Master` 这个就是对称密钥，当然客户端也计算出了同样的`main-master`密钥，之后的通讯就直接通过这个密钥进行对称加密通讯了

因为对称密钥协商算法的参数和第三个随机数都是经过非对称加密传输的，那么中间人就无法破解出对称密钥

> 按道理只传输最后一个`pre-master`随机数即可，但是为什么要选择再传输前两个随机数呢？
>
> **选择三个随机数是为了加大随机性，三个随机数拼起来的随机性更大**

### 2、Change Cipher Spec和Finished

> `Encrypted Handshake Message`和`Change Cipher Spec`作用一样，在ECDHE中使用前者，而在RSA中使用后者

client finished

- 客户端发送一个`Change Chipher Spec`表示随后的信息都将用双方商定的加密方法和密钥发送
- 然后再发送 `Finished`目的是把之前所有发送的数据做个摘要，再使用对方的公钥加密一下让服务器做个验证，验证通过则握手完成

server finished

- server对client的`Finished`解密同时进行校验，校验通过则回复一个`Change Chipher Spec`
- 最后再发送一个`Finished`表示TLS握手结束，之后的所有HTTP报文都使用TLS握手生成的对称密钥进行加密通讯

​     

## ECDHE算法

`ECDHE`算法是一个对称密钥协商算法，比`RSA`算法要快并且更加安全，在实际使用过程中应该选择`ECDHE`

总结起来对称密钥协商过程就是通过非对称加密传递密钥生成算法的公式和参数然后双方就可以通过相同的公式和参数计算出相同的密钥，之后就可以直接使用对称密钥进行传输数据了   

​    

## TLS会话恢复

### SessionID

上面的TLS握手过程非常复杂，可以通过 `Session ID`来优化

生成对称密钥的时候会服务器会传递给客户端一个SessionID，同时双方都会在内存中保存密钥，下次客户端再次发送`Client Hello`的时候就可以带上`SessionID`  ，服务器会查找对应的Session信息，直接使用之前协商出来的对称密钥进行传输，这样就不必每次都协商对称密钥了

为了保障安全，服务器中的Session会定期失效，如果失效了则还需要进行TLS握手

此方法有下面两个缺点:

- 如果服务器是集群，则客户端访问可能不会命中保存Session的服务器
- 服务器保存所有的Session造成服务器压力大

### SessionTicket

我们可以将Session信息经过服务器的公钥加密然后保存在客户端，加密之后的密钥信息就是叫: `SessionTicket` 

下次客户端发送`Client Hello`的时候就带上这个SessionTicket，服务器通过私钥进行解密，这样就知道Session信息了，这样双方也就可以使用之前的密钥了

​    

## 参考

[如何用 wireshark 抓包 TLS 封包](https://segmentfault.com/a/1190000018746027)

[2分钟掌握 HTTPS原理和 TLS 握手机制](https://www.bilibili.com/video/BV1Ef4y1R7FW?from=search&seid=1394733582429779729)

https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html

[https://cshihong.github.io](https://cshihong.github.io/2019/05/09/SSL协议详解/)