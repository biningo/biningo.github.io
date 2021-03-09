---
title: HTTP协议
date: 2021-03-08
categories: [网络]
tags: [HTTP]
draft: true
---

## 请求和响应报文

请求报文第一行必须指定 **请求的方法、资源路径、HTTP协议版本**

紧接着就是请求头信息

然后还需要一个空行

紧接着如果有请求体的话就是请求体，注意如果没有请求体也必须加一个空行

```http
GET /note/ef1b6cee.html HTTP/1.1
Host: www.ru23.com
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) 
....各个请求头
<空行>
```

响应报文第一行必须指定 **HTTP协议版本、状态码、状态码的字符串简短描述**

紧接着就是响应头信息

然后还需要一个空行

如果有响应体的化就需要一个响应体，没有的话空行也是必须的

```http
HTTP/1.1 200 OK
Server: nginx/1.16.1
Date: Sun, 04 Oct 2020 02:27:01 GMT
Last-Modified: Sun, 30 Aug 2020 14:12:42 GMT
Content-Type: text/html
Content-Length: 40846
Accept-Ranges: bytes

<!DOCTYPE html>
<html>
<head><meta name="generator" content="Hexo 3.8.0"></head>
    <body>
    	<div>
    	.....
    	</div>
    </body>
</html>
```

​    

## Body压缩

报文压缩、加密等都是对于HTTP报文的`Body`部分进行的，**压缩和加密** 其实都是对Body的一种编码方式，比如启用 `gzip`压缩就是对Body部分进行`gzip`格式的编解码，这样就达到了压缩的效果，进行压缩或则加密的报文都需要消耗一定的CPU资源

压缩`Body`的时候服务器需要在响应的Header指定`Content-Encoding`:

```http
HTTP/1.1 200 ok
Content-Encoding: gzip
....
```

这样客户端就可以根据这个字段来知道Body是否被编码，以此来解码

客户端在请求的时候也可以设置Header指定`Accept-Encoding`字段表明自己可以接受哪些编码方式，服务器就可以根据客户端的请求来改变相应的响应策略

```http
GET /a/b.html HTTP/1.1
Accept-Encoding: gzip,deflate,compress
...
```

​    

## Cookie

HTTP是无状态的，但是我们可以通过 **Cookie** 来保存一些状态数据等

Cookie是浏览器的特性，在移动端APP中使用HTTP协议请求后端服务就没有Cookie这一说法，移动端APP中不需要设置Cookie

服务器需要在响应报文头里面设置`Set-Cookie`字段，浏览器就会根据这个字段的信息来设置相关的Cookie数据保存在客户端本地，下次客户端请求的时候就会通过请求报文头的`Cookie`字段将本地的Cookie数据设置进去，这样服务器就可以拿到自己设置的Cookie数据了

下面是服务器响应报文，Cookie是键值对形式，并且可以设置Cookie的保存时间，如果需要设置多个Cookie则需要多个Set-Cookie字段

```http
HTTP/1.1 200 ok
...
Set-Cookie: username=lyer;Max-Age=1000
Set-Cookie: age=18;Expires=Fri,07-Jun-19:00 GMT;HttpOnly;Secure
...
```

- `Expires` 失效的具体时间

- `Max-Age` 需要保存的秒数，设置为 **<=0** 表示不保存，可以用此来主动让浏览器删除Cookie，优先级高于Expires

- `Domain`和`Path`  指定了哪些域名和路径能使用这个Cookie，浏览器在发送 Cookie 前会将请求的Host和URL与Cookie的相关属性进行对比，如果满足条件才会在请求头里发送 Cookie

    所有以Path为前缀的路径都会被匹配，如果没有指定域名则默认是设置Cookie的域名，Path设置为`"\"`则表示此应用下的所有路径都可以使用Cookie

- `HttpOnly` 此 Cookie 只能通过浏览器 HTTP请求进行传输，禁止其他方式访问（比如用`document.cookie`等js 函数API来访问这是被禁止的），可以防止各种**脚本攻击**

- `Secure` 表示此Cookie只能在HTTPS下才能发送到服务器

**注意: Cookie的大小最大限制为4k**

另外Session也是通过Cookie来实现的，服务器会设置一个`SessionID`到Cookie中，这样没次客户端就会带上这个`SessionID`，服务器就可以根据SessionID再去数据库查询用户的状态信息

> **第三方Cookie**

Cookie使用的时候还需要注意第三方Cookie，第一方和第三方的唯一区别只是`Set-Cookie`中的`domain` 是否和被访问网站的域一样，是就是第一方，否就是第三方

比如你访问网站 `www.baidu.com`的时候，网站在你的电脑上设置了一个Cookie，里面的记录的域名也是 `www.baidu.com`，那么这个Cookie就是第一方的，归你访问的网站 `www.baidu.com`所有。而如果你访问网站`www.baidu.com`时，在你的计算机中设置的 Cookie的域名是`www.google.com`，那么这个Cookie就是第三方Cookie，归`www.google.com`所有

第三方Cookie可以实现如下几个功能:

- **单点登入** 

​    

## HTTP内容协商



## Chunk传输

每个HTTP响应报文都必须在Header中指定`Content-Length`，此长度就是`Body`的长度，浏览器会根据这个头部去解析读取Body的数据，如果这个长度小于Body，那么就无法完全读取Body的数据，如果大于Body的数据那么就会报错，如果不指定`Content-Length`那么，则必须指定另外一个头部`Transfer-Coding:chunked`，否则浏览器就会一直处于Pending状态等待

`Transfer-Coding:chunked`表示响应的报文Body长度是不确定的，无法计算出来，`Body`数据是一部分一部分传输的，直到遇到结束符浏览器就可以拼接之前接受到的分块Body，有了`Transfer-Coding:chunked` 之后浏览器就会忽略`Content-Length`头部

分块传输的结构如下:

<img src="https://raw.githubusercontent.com/biningo/cdn/master/img1/chunked.png" style="zoom:50%;" />

下面用Go来模拟一下Chunked传输:

```go
func chunkedHandler(conn net.Conn){
	conn.Write([]byte("HTTP/1.1 200 OK\r\n"))
	conn.Write([]byte("Transfer-Encoding: chunked\r\n"))
	conn.Write([]byte("\r\n"))

	conn.Write([]byte("6\r\n"))
	conn.Write([]byte("hello,\r\n"))
	time.Sleep(time.Second)

	conn.Write([]byte("8\r\n"))
	conn.Write([]byte("chunked!\r\n"))
	time.Sleep(time.Second)
	conn.Write([]byte("0\r\n"))
	conn.Write([]byte("\r\n"))
}

//main
func main() {

	ln, err := net.Listen("tcp", ":9090")
	if err != nil {
		log.Fatal(err)
	}

	for {
		conn, err := ln.Accept()
		if err != nil {
			log.Fatal(err)
		}
		log.Println("------------------")
		go chunkedHandler(conn)
	}

}
```

​    

## HTTP长连接keep-alive

TODO



## 参考

- https://www.cnblogs.com/lightsong/p/4172979.html 【第三方Cookie】
- https://www.bwangel.me/2018/11/01/http-chunked 【Chunked分块传输】