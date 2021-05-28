---
title: TSL(SSL)协议
date: 2021-03-16
categories: [网络]
tags: [TSL/SSL,网络安全,HTTPS]
---

## 什么是TSL协议

因为HTTP传输都是明文传输的，如果被中间人截获，那么报文内容就会被盗取

为了防止HTTP明文传输，于是可以讲HTTP报文和数据整个进行加密然后再将数据传递给下层的TCP层，传递到客户端再进行解密，`TSL`协议就是解决HTTP报文加密问题的，被TSL协议加密之后的HTTP协议就叫`HTTPS` **HTTP over SSL** 使用的是`443`端口

> TSL是SSL的改进版，现在都是使用TSL来替换SSL了，但是延续了老的叫法，所有都叫`SSL` 这两个其实是同一个意思
>
> TSL现在广泛使用的版本为`TSL1.2` `TSL1.3`

​    

## TSL握手第一阶段

加密算法有 **对称加密、非对称加密** ，如果将HTTP报文采用非对称加密的话那么带来的CPU运算是非常大的，速度也相对较慢，因为HTTP报文一般数据比较大

所以TSL采用对称加密的方式来加密HTTP报文加快加密解密的速度，但是这里就涉及到如何传递 **对称密钥** 的问题了，这就是 **TSL握手** 需要解决的问题，就是生成对称密钥

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/tsl.png)

### Client Hello

TCP建立之后浏览器会先发送一个`Client Hello` TSL报文给服务器，目的是为了告诉服务器如下内容，重点关注如下内容:

- 客户端TSL版本
- **支持的加密套件Cipher Suites** : 使用什么加密算法等，服务器会选择一个加密算法
- **随机字符串Random1** : 这个随机数和之后的生成对称密钥有关
- **Session ID**  用来**恢复会话** 
- **SessionTicket** 客户端保存的TSL的session信息

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/ClientHello.png)

### Server Hello

服务器接受到客户端的`Client Hello`之后就会检查客户端版本、加密套件等信息，检查通过并且符合服务器的条件则服务器会进行回复，也就是发送`Server Hello`包

回复内容主要是告诉客户端如下几个重要的内容:

- 服务器的TSL版本

- 从`Client Hello`中选择一个加密套件，告诉服务器选择了哪个套件
- **随机字符串Random2** 此时客户端和服务器都拥有 `Random1、Random2`
- **Session ID** 初次握手服务器会返回一个Session ID，客户端会保存这个ID，之后再进行握手发送Client Hello的时候就会携带上这个Session ID，服务器就会直接查询Session ID对应的信息比如对称加密的密钥，这样就可以直接复用了就不需要再次生成了，因为生成过程也有一定的消耗

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/ServerHello.png)

​    

## TSL握手第二阶段

第二阶段，双方已经确定了:

- **双方协商的非对称加密的算法** (比如RSA)
- **Random1**
- **Random2**

这一阶段中，全部都是服务器在发送消息，服务器会连发还几个包

### Certificate

服务器的证书，客户端收到之后需要进行验证，然后就可以拿到服务器证书里面的公钥了

### Server Key Exchange

服务器传递计算密钥算法的一些参数，这样他们才可以根据相同的参数和算法计算出相同的 **对称密钥**

这些参数用服务器自己的私钥进行加密然后传输给客户端，客户端可以用服务器的公钥进行解密获取到参数

### Server Hello Done

表示服务器的信息发送完毕

​    

## TSL握手的第三个阶段

### Client Key exchange

客户端收到算法计算公式的参数之后再次生成一个随机数`pre-master`，此随机数就必须要用服务器的公钥加密之后发给服务器，服务器收到到`pre-master`之后即可以 结合 **Random1、Random2、Pre-Master** 和加密算法的公式再次计算出 `main-master` 这个就是 **对称密钥**

当然客户端也计算出了同样的`main-master`密钥，之后的通讯就直接通过这个密钥进行对称加密通讯了

因为对称密钥协商算法的参数和第三个随机数都是进过非对称加密传输的，那么黑客就无法破解出对称密钥

按道理只传输最后一个`pre-master`随机数即可，但是为什么要选择再传输前两个随机数呢？

**选择三个随机数是为了加大随机性，三个随机数拼起来的随机性更大**

### Finished和Change Cipher Spec

客户端发送一个 **change chipher spec** 表示随后的信息都将用双方商定的加密方法和密钥发送

然后再发送 **finished**  目的是把之前所有发送的数据做个摘要，再加密一下，让服务器做个验证，验证通过则握手完成

服务器也是同样的操作，发“**Change Cipher Spec**”和“**Finished**”消息，双方都验证加密解密 OK，握手正式结束，后面就收发被加密的 HTTP 请求和响应了

​        

## DH算法

DH算法是一个对称密钥协商算法，为了理解上面的 **对称密钥协商**，我们以简单的 `DH` 算法为例

1. 甲首选选择一个素数`p`，例如509，底数`g`，任选，例如5，随机数`a`，例如123，然后计算`A=g^a mod p`，结果是215，然后，甲发送`p＝509`，`g=5`，`A=215`给乙
2. 乙方收到后，也选择一个随机数`b`，例如，456，然后计算`B=g^b mod p`，结果是181，乙再同时计算`s=A^b mod p`，结果是121 
3. 乙把计算的`B=181`发给甲，甲计算`s＝B^a mod p`的余数，计算结果与乙算出的结果一样，都是121 

```bash
参数p、g
A、a->Random1
B、b->Random2
s->main-master
```

总结起来对称密钥协商过程就是通过非对称加密传递密钥生成算法的公式和参数然后双方就可以通过相同的公式和参数计算出相同的密钥，之后就可以直接使用对称密钥进行传输数据了   

​    

## TSL握手优化

### SessionID

上面的TSL握手过程非常复杂，可以通过 `Session ID`来优化

生成对称密钥的时候会服务器会传递给客户端一个SessionID，同时在服务区端保存了对应的Session信息比如此次对话的对称密钥

下次客户端再次发送`Client Hello`的时候就可以带上`SessionID`  服务器查找Session信息，直接使用之前协商出来的对称密钥进行传输，这样就不必每次都协商对称密钥了

### SessionTicket

上面的Session信息都保存在服务区，如果客户端很多则会造成服务器消耗很大，所以我们可以讲Session信息经过服务器的公钥加密然后保存在客户端(服务器公钥加密的信息谁都无法解密出来)，这个信息就叫`SessionTicket` 

下次客户端发送`Client Hello`的时候就带上这个SessionTicket，服务器通过私钥进行解密，这样就知道Session信息了

​    

## 参考

https://www.ruanyifeng.com/blog/2014/02/ssl_tls.html

[https://cshihong.github.io](https://cshihong.github.io/2019/05/09/SSL协议详解/)