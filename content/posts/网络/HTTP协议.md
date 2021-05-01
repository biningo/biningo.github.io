---
title: HTTP协议
date: 2021-03-08
categories: [网络]
tags: [HTTP]
---

## 请求和响应报文

- 请求报文第一行必须指定 **请求的方法、资源路径、HTTP协议版本**

- 紧接着就是请求头信息

- 然后还需要一个空行

- 紧接着如果有请求体的话就是请求体，注意如果没有请求体也必须加一个空行

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

- 响应报文第一行必须指定 **HTTP协议版本、状态码、状态码的字符串简短描述**

- 紧接着就是响应头信息

- 然后还需要一个空行

- 如果有响应体的化就需要一个响应体，没有的话空行也是必须的

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

## URL编码

如果在浏览器中输入的URL含有特殊字符或则非`ASCII`比如中午，浏览器则会进行转义编码操作，编码方法就是将非ASCII或则特殊字符转化为 **16进制**，然后在前面加一个`%`

比如中文的话则会工具UTF8编码的二进制转化为16进制然后加一个`%`，比如:

```bash
%E9%93%B6%E6%B2%B3
```

如果我们收到一个URL编码的query参数，那么可以用上述方法进行解码

​    

## HTTP请求方法

> **请求方法单词必须全部大写**

1. **GET**：获取资源，可以理解为读取或者下载数据
2. **HEAD**：获取资源的元信息，只返回HTTP头部信息，不返回Body
3. **POST**：向资源提交数据，上传的数据在Body中，相当于写入或上传数据
4. **PUT**：更新资源，整个替换
5. **PATCH**: 跟新资源，部分替换
6. **DELETE**：删除资源
7. **CONNECT**：建立特殊的连接隧道
8. **OPTIONS**：列出服务器支持那些类型的HTTP请求，比如GET、POST，由响应报文头部的`Allow`字段列出

​    

## HTTP常见状态码

状态码主要分为如下`5`类:

| 类型 | 含义                                                         |
| ---- | ------------------------------------------------------------ |
| 1xx  | 请求还需要进一步处理                                         |
| 2xx  | 请求成功                                                     |
| 3xx  | 需要重定向，通过`Location`来指定重定向的URL，重定向如果只是路径的话则是站内跳转，如果是完整的URL则是站外跳转`Location:/ping` `Location:http://www.google.com` |
| 4xx  | 客户端错误                                                   |
| 5xx  | 服务器端错误                                                 |

下面是常见的响应码:

| 响应码 | 含义                                                         | 描述                |
| ------ | ------------------------------------------------------------ | ------------------- |
| 101    | 如果请求报文携带了`Upgrade`要求升级协议,比如升级为`websocket`，如果服务器返回`101`则表示同意升级协议 | Switching Protocols |

| 响应码 | 含义                                                         | 描述            |
| ------ | ------------------------------------------------------------ | --------------- |
| 200    | 成功响应请求                                                 | OK              |
| 201    | 新资源在服务器成功创建                                       | Created         |
| 202    | 服务器开始处理请求，但是没有完成(异步任务、长时间处理的任务) | Accepted        |
| 204    | 成功响应请求，并且没有数据返回Body为空，暗示客户端不需要进行视图数据更新 | No Content      |
| 206    | 成功响应请求，但是Body的数据只是一部分，用于 **断点续传**  响应报文用`Content-Range`来标识当前数据的范围，属于哪一部分数据，如`Content-Range: bytes 0-99/2000` 意思是此次获取的是总计 2000 个字节的前 100 个字节 | Partial Content |

| 响应码 | 含义                                                         | 描述               |
| ------ | ------------------------------------------------------------ | ------------------ |
| 301    | 永久重定向，资源永久的改变了，浏览器可以缓存，比如HTTP永久重定向到HTTPS | Moved Permanently  |
| 308    | 和`301`功能一样，但是规定重定向请求方法必须和之前的方法一样  | Permanent Redirect |
| 302    | 临时重定向，资源暂时移动，浏览器不会进行缓存                 | Found              |
| 307    | 和`302`功能一样，但是规定重定向请求方法必须和之前的方法一样  | Temporary Redirect |
| 304    | 缓存重定向，表示资源未修改，可是使用浏览器本地缓存           | Not Modified       |

| 响应码 | 含义                                                         | 描述                                       |
| ------ | ------------------------------------------------------------ | ------------------------------------------ |
| 400    | 客户端未知错误                                               | Bad Request                                |
| 401    | 认证信息缺失或不正确，导致服务器无法处理请求                 | Unauthorized                               |
| 403    | 权限不足，禁止访问资源                                       | Forbidden                                  |
| 404    | 资源未找到                                                   | Not Found                                  |
| 410    | 资源未找到，并且服务器明确知道该资源永久性找不到             | Gone                                       |
| 405    | 不支持请求方法，比如不支持POST请求                           | Method Not Allowed                         |
| 406    | 对客户端指定的资源表述不存在（例如对语言或者编码有要求） 服务器返回表述列表供客户端选择 | Not Acceptable                             |
| 408    | 服务器处理请求超时                                           | Request Timeout                            |
| 409    | 资源冲突，比如重复上传一个已经存在的文件                     | Conflict                                   |
| 412    | 复用缓存时传递的 `If-Unmodified-Since` 或 `If-None-Match` 头部不被满足 | Precondition Failed                        |
| 413    | 请求Body超出服务器所能接受的最大长度                         | Payload Too Large/Request Entity Too Large |
| 414    | 请求URL超出服务器能处理的最大长度                            | URI Too Long                               |
| 415    | 上传的文件类型不被支持                                       | Unsupported Media Type                     |
| 416    | 无法提供`Range`指定的数据                                    | Range Not Satisfiable                      |
| 417    | 无法满足`Expect`                                             | Expectation Failed                         |
| 426    | 必须要求升级协议，服务器通过`Upgrade字段`通知客户端必须要升级的协议 | Upgrade Required                           |
| 428    | 用户请求中缺失了条件类头部，例如 `If-Match`                  | Precondition Required                      |
| 429    | 客户端发送请求频率过快                                       | Too Many Requests                          |
| 431    | 请求的Header头部信息太大                                     | Request Header Fields Too Large            |

| 响应码 | 含义                                                         | 描述                  |
| ------ | ------------------------------------------------------------ | --------------------- |
| 500    | 服务器内部未知错误                                           | Internal Server Error |
| 501    | 客户端请求的功能还不支持，"**即将推出，尽请期待**"           | Not Implemented       |
| 502    | **代理服务器**无 法获取到合法响应，代理服务器正常，后端服务器发生错误 | Bad Gateway           |
| 503    | 服务器当前正忙，暂时无法响应服务，503 响应报文里通常还会有一个 `Retry-After` 字段，指示客户端可以在多久以后再次尝试发送请求 | Service Unavailable   |

​    

## HTTP内容协商

内容协商是指客户端希望得到什么类型的数据或则希望采用什么编码，服务器需要根据客户端的请求来做出合理的响应

客户端和内容协商的请求头都以 `Accept-`开头，服务器响应内容协商的相关头部都以`Content-`开头

`Accept` 允许的响应MINE类型

 `Content-Type`服务器响应的MINE类型，一般服务器的编码会在Cotent-Type头部指定，浏览器会根据`chartset`指定的编码来进行解码，默认都是UTF-8编码，所以如果Content-Type指定的charset和传递数据的编码方式不一致的话就会产生乱码

```bash
#q表示质量因子 也就是权重
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
```

```bash
#浏览器会采用charset中的编码方式进行解码
Content-Type: text/html; charset=utf-8 
```

`Accept-Encoding` 允许的内容编码 ，主要用于压缩  `Content-Encoding`  服务器采用的压缩编码

```bash
Accept-Encoding: gzip, deflate, br
```

```bash
Content-Encoding: gzip
```

`Accept-Language` 允许的语言 `Content-Language`服务器响应的语言

```bash
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
```

```bash
Content-Language: zh-CN
```

另外服务器必须指定`Content-Length`来表示响应Body的长度，如果是 **Chunk**传输不知道长度则必须指定`Transfer-Encoding:chunked`

```bash
Content-Length: 1000 #byte
```

`Vary` 列出了服务器参考了哪些Header字段来进行内容协商

```bash
Vary: Accept-Encoding,Accept
```

常见的的 **MINE** 类型如下:

| MINE                                                         | 描述       |
| ------------------------------------------------------------ | ---------- |
| `text/html` `text/css` `text/plain`纯文本                    | 文本类型   |
| `image/gif` `image/png` ...                                  | 图片类型   |
| `audio/mp4` `audio/mp3` ...                                  | 音视频类型 |
| `application/json`  `application/pdf` `application/javascript` `application/octet-stream`未知的二进制流类型 | 应用类型   |

​    

## HTTP头部字段和常见头部

| 类型     | 含义                                                     |
| -------- | -------------------------------------------------------- |
| 通用字段 | 在请求头和响应头里都可以出现                             |
| 请求字段 | 仅能出现在请求头里，进一步说明请求信息或者额外的附加条件 |
| 响应字段 | 仅能出现在响应头里，补充说明响应报文的信息               |
| 实体字段 | 属于通用字段，但专门描述 body 的额外信息                 |

头部字段是不区分大小写的，比如`Host`和`host`是一样的，但是一般建议使用大写，并且我们不仅仅可以使用HTTP规范的头部，还自定义头部字段，自定义头部需要注意以下几个规则:

- 头部字段名字不允许出现空格，可以允许 `-` 来分割不同的单词，但是不允许使用 `_` 下划线，比如`User-Name`是合法的，`User_Name`就是非法的
- 字段名后面必须紧接着`:`，不能有空格
- 头部字段顺序没有意义，可以随意排列
- 有些头部字段可以重复设置例如 `Set-Cookie`，有些则只能设置一个比如`Host`

**下面来看一些常见头部**:

`User-Agent` 客户端类型，浏览器类型，浏览器内核版本信息，操作系统类型等

```bash
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36
```

`Host` 表示请求的域名，DNS获取到IP只是请求到那台机器，因为一个机器可能会开好几个服务和域名，所以需要Host来标识是哪个服务

```bash
Host: v.qq.com
```

> nginx中配置的`server_name`需要和HTTP头部的`Host`字段相匹配这样才可以访问，如果不匹配则会走默认的server_name，这样可以实现一台主机多个域名

`Date` 表示报文创建的时间，**GMT** 格式

```bash
Date: Sun, 04 Oct 2020 02:27:01 GMT
```

`Referer` 求来自哪个页面，由浏览器来自动添加

```bash
Referer:https://www.google.com
```

`Server` 服务器类型

```bash
Server: nginx
```

`Allow` 服务器运行的请求类型

```bash
Allow: GET,POST,OPTIONS      
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
Set-Cookie: age=18;Expires=Fri,07-Jun-19:00 GMT;HttpOnly;Secure;Domain=www.google.com;Path=/;SameSite=Strict
...
```

- `Expires` 失效的具体时间

- `Max-Age` 需要保存的秒数，设置为 **<=0** 表示不保存，可以用此来主动让浏览器删除Cookie，优先级高于`Expires`

- `Domain`和`Path`  指定了哪些域名和路径能使用这个Cookie，浏览器在发送 Cookie 前会将请求的Host和URL与Cookie的相关属性进行对比，如果满足条件才会在请求头里发送 Cookie

    所有以Path为前缀的路径都会被匹配，如果没有指定域名则默认是设置Cookie的域名，Path设置为`"\"`则表示此应用下的所有路径都可以使用Cookie

- `HttpOnly` 此 Cookie 只能通过浏览器 HTTP请求进行传输，禁止其他方式访问（比如用`document.cookie`等js 函数API来访问这是被禁止的），可以防止各种**脚本攻击**

- `Secure` 表示此Cookie只能在HTTPS下才能发送到服务器

- `SameSite`  

**注意: Cookie的大小最大限制为4k**

另外`Session`也是通过Cookie来实现的，服务器会设置一个`SessionID`到Cookie中，这样没次客户端就会带上这个SessionID，服务器就可以根据SessionID再去数据库查询用户的状态信息，这样就实现免登入了，同时SessionID应该设置一个过期时间

> **第三方Cookie**

Cookie使用的时候还需要注意第三方Cookie，第一方和第三方的唯一区别只是`Set-Cookie`中的`domain` 是否和被访问网站的域一样，是就是第一方，否就是第三方

比如你访问网站 `www.baidu.com`的时候，网站在你的电脑上设置了一个Cookie，里面的记录的域名也是 `www.baidu.com`，那么这个Cookie就是第一方的，归你访问的网站 `www.baidu.com`所有。而如果你访问网站`www.baidu.com`时，在你的计算机中设置的 Cookie的域名是`www.google.com`，那么这个Cookie就是第三方Cookie，归`www.google.com`所有

第三方Cookie可以实现如下几个功能:

- **单点登入** 

​    

## HTTP文件下载

HTTP下载文件时必须要设置的两个头部:

`Content-Type` 用于指定待下载文件的类型

`Content-Disposition` 用于指定下载后文件名以及相应的编码规则

- `attachment`表示浏览器会将数据下载下来，如果不设置则不会进行下载
- `inline` 表示响应中的消息体会以页面的一部分或者整个页面的形式展示

```bash
Content-Type: application/pdf
Content-Disposition: attachment; filename="filename.jpg" 
```

​    

## Chunk传输

每个HTTP响应报文都必须在Header中指定`Content-Length`，此长度就是`Body`的长度，浏览器会根据这个头部去解析读取Body的数据，如果这个长度小于Body，那么就无法完全读取Body的数据，如果大于Body的数据那么就会报错，如果不指定`Content-Length`那么，则必须指定另外一个头部`Transfer-Coding:chunked`，在遇到结束chunk传输的结束标记之前浏览器就会一直处于Pending状态等待服务器传输分块的数据，注意这些分块的数据不需要头部，但是每个分块都会标识该块的数据长度

`Transfer-Coding:chunked` 还可以表示响应的报文Body长度是不确定的，数据是动态生成的，无法计算出来，`Body`数据分块，直到遇到结束符浏览器就可以拼接之前接受到的分块Body，有了`Transfer-Coding:chunked` 之后浏览器就会忽略`Content-Length`头部

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

## HTTP范围请求

HTTP的范围请求可以将一个数据分为好几段来请求，或则只请求数据中的某段数据。比如视频传输中如果用户拉动进度条跳到视频的中间部分，那么就只需要将视频中间部分传输给客户端即可而不需要将整个视频都传过去。在大文件下载或则上传中如果网络断开或则其他原因导致下载上传失败，如果使用分段下载的话则每次失败就不用将全部的文件再从头下载了，这就是 **断点续传**

范围请求需要我们自己在服务器和客户端实现，需要设置几个请求头，然后实现相关的处理逻辑

`Accept-Range` 表示服务器是否支持范围请求，属于 **响应头部** 如果值为`none`或则没有这个响应头部则表示服务器不支持范围请求

```bash
Accept-Range: none #不支持
Accept-Range: bytes #支持
```

`Range` 客户端请求的字节范围，从0开始计数

```bash
Range: bytes=0-10 #[0,10]一共11个字节
Range: bytes=10- #[10,] 10字节之后所有
Range: bytes=-500  #最后500字节
```

`Content-Range` 表示服务器返回的数据的范围，状态码`206`

```bash
Content-Range: bytes 0-31/96 #96是数据的全部大小
```

`Content-Range` 也允许一次请求多个片段，这个和 **Chunked、form** 等数据传输格式很相似，`Content-Length`则表示多个片段数据的总和长度，另外还必须指定`Content-Type:multipart/byteranges; boundary=00000000001` 后面的`boundary`是多段数据的分割符

```http
GET /16-2 HTTP/1.1
Host: www.xxx.com
Range: bytes=0-9, 20-29
```

```http
HTTP/1.1 206 Partial Content
Content-Type: multipart/byteranges; boundary=00000000001
Content-Length: 189
Connection: keep-alive
Accept-Ranges: bytes
 
--00000000001
Content-Type: text/plain
Content-Range: bytes 0-9/96
 
 1234567890
--00000000001
Content-Type: text/plain
Content-Range: bytes 20-29/96
 
 abcdefghij
--00000000001--
```

范围请求下载文件步骤如下:

- 先发送`HEAD`请求查看服务器是否支持范围请求
- 请求指定范围数据，如果超过了数据的范围，则服务器返回`416` 
- 如果条件符合，则服务器读取`Range`信息计算数据的偏移量读取读取文件片段并且在相应头部需要设置`Content-Range` 来标识这个数据属于哪一个范围

基于HTTP范围请求的规范和Header，就可以实现 **断点续传、视频拖拽、多段并发下载** 

- 客户端发送一个HEAD请求查看服务器是否支持范围请求，并且获取数据的总体大小
- 开N个线程，并且使用Range分段，然后并发下载
- 下载失败只需要重新下载失败的那一块数据即可，不需要全部再重新下载

​        

## FORM表单数据

`Content-Type`在Post提交的时候也会用到，告诉服务器提交的信息属于Form表单编码还是JSON格式，并且POST请求也需要指定`Content-Length`

HTML表单里可以根据`<form enctype="application/form-data">` 来指定表单上传的编码方式，POST提交的数据主要有如下几个类型

```bash
Conent-Type: mutipart-data
Content-type: x-www.form-urlencoded #form不指定enctype默认就是这种编码
Conent-Type: application/json
```

### 1、x-www-form-urlencoded

提交的表单数据按照 `key=val&key=val`方式进行编码

```http
POST http://www.example.com HTTP/1.1
Content-Type: application/x-www-form-urlencoded;charset=utf-8

name=lyer&age=18
```

### 2、multipart/form-data

需要在`Content-Type`中指定`boundary`分隔符

```bash
POST http://www.example.com HTTP/1.1
Content-Type:multipart/form-data; boundary=-------XXXX

-------XXXX
Content-Disposition: form-data; name="username"

lyer
-------XXXX
Content-Disposition: form-data; name="age"

18
-------XXXX
Content-Disposition: form-data; name="file"; filename="a.png"
Content-Type: image/png

<二进制数据>
-------XXXX
```

​      

## HTTP长连接keep-alive

`Connection` 默认值是`keep-alive`

```bash
Connection:close #立即关闭此次TCP连接 即不支持长链接
```

​    

## HTTP队首阻塞

HTTP是 **请求-应答** 模式，如果同时发送多个请求那么当浏览器收到多个应答的时候就无法辨别那个应答属于哪个请求，所以HTTP协议必须是一收一发的

**所以每个请求需要加入一个请求队列，只有等前面请求的应答收到之后此请求才可以发送**

如果队首有请求一致阻塞了，那么后面所有的请求都必须等待，这就是 **队首阻塞问题**

为了解决队首阻塞问题造成的速度慢，有如下几个方法:

- 浏览器多开几个线程并发进行请求后端服务器资源，此方法问题就是后端服务器接收到的并发数量巨大: **用户数并发请求线程数** 造成服务器压力太大，所以一般浏览器只会开`6~8`个线程左右的进行并发请求
- **域名分片** 也就是给一台机器一个服务多开几个域名，这样分别将用户的请求负载均衡到各个域名上去请求同一台机器，这样并发也上去了

​    

## HTTP代理

`Via` 保存经过的代理节点的主机名字或则域名

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/image-20201007095736179.png)

```bash
Via: nginx-1,nginx-2
```

`X-Forwarded-For` 保存经过代理的IP

```bash
X-Forwarded-For: 192.168.0.10,192.168.0.12
```

​        

## HTTP缓存和条件请求

### 1、Cache-Control 强缓存

`Cache-Control` 控制浏览器缓存的时间，注意此头部是通用头部，请求和响应都可以有，如果是服务器响应头部的话则是告诉浏览器此信息可以缓存多久

一般点击 **刷新、地址栏重新输入、ctrl+f5** 则浏览器会制动设置Cache-Control的值为`max-age=0` 或则 `no-cache` ，这两个意思差不多，表示不从缓存取，直接请求服务器

如果是 **超链接、重定向、或则被动请求** 则不会自动加上头部，那么如果没有超过max-age的值得话就会直接从缓存中获取，这就是 **强缓存** 即使服务器资源改变了，只要没有超过max-age则就会从缓存中拿

| Cache-Control值     | 含义                                                         |
| ------------------- | ------------------------------------------------------------ |
| max-age             | 单位是秒，缓存的开始时间是报文创造时的时间                   |
| no-cache(max-age=0) | 不使用强缓存，直接请求服务器，协商缓存，如果服务器返回`304`表示缓存可用，重定向到缓存 |
| no-store            | 禁止使用缓存，也不会进行协商缓存                             |
| private             | 专用于个人的缓存，中间代理、CDN 等不能缓存此响应             |
| public              | 响应可以被中间代理、CDN 等缓存                               |
| must-revalidate     | 在缓存过期前可以使用，过期后必须向服务器验证，进行协商缓存   |
| no-transform        | 不允许中间代理服务器对资源做转化                             |

### 2、协商缓存

如果缓存时间过了，但是服务器资源还是没有改变，那么就还是可以继续用缓存的，而不需要服务器再次传递数据

当浏览器的强缓存失效的时候或者请求头中设置了`Cache-Control:max-age=0|no-cache` 不走强缓存，并且在请求头中设置了`If-Modified-Since` 或者 `If-None-Match` 的时候，会将这两个属性值到服务端去验证是否命中协商缓存，如果命中了协商缓存，会返回 `304` 状态，加载浏览器缓存，并且响应头会设置 `Last-Modified` 或者 `ETag` 属性

请求头部:

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/http-cache.png)

响应头部:

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/http-cache2.png)

`Last-Modified/If-Modified-Since`  的值代表的是文件的最后修改时间，第一次请求服务端会把资源的最后修改时间放到 Last-Modified 响应头中，**第二次发起请求的时候，请求头会带上上一次响应头中的 Last-Modified 的时间，并放到 If-Modified-Since 请求头属性中**，服务端根据文件最后一次修改时间和 If-Modified-Since 的值进行比较，如果相等，返回 304 ，并加载浏览器缓存

`ETag/If-None-Match` 是为了解决上面两个头部无法解决的问题:

- 资源改动间隔时间为秒级，无法通过时间来判断缓存是否改变，因为max-age的单位为`s`
- 资源更新了，但是内容还是之前的内容，这种情况还是可以继续使用缓存的，所以Etag也是不会变的

`ETag/If-None-Match` 的值是一串 hash 码，代表的是一个资源的标识符，当服务端的文件变化的时候，它的 hash码会随之改变，通过请求头中的 `If-None-Match` 和当前文件的 hash 值进行比较，如果相等则表示命中协商缓存

ETag 又有强弱校验之分，如果 hash 码是以 `W/` 开头的一串字符串，说明此时协商缓存的校验是弱校验的，只有服务器上的文件差异（根据 ETag 计算方式来决定）达到能够触发 hash 值后缀变化的时候，才会真正地请求资源，否则返回 304 并加载浏览器缓存

​    

## 同源策略和跨域请求

### 1、何为同源？何为跨域？

首先需要知道什么是 **同源和非同源**

同源: **域名、协议、端口** 完全一致就是同源头

不同源之间相互访问就叫 **跨域访问**

```bash
google.com www.google.com #域名不一致 不同源
google.com:8080 google.com:9090 #端口不一致 不同源
http://google.com https://google.com #协议不一致 不同源
google.com/b.html google.com/c.html #同源
```

### 2、什么是同源策略?

浏览器对于非同源请求做出了如下限制，也就是同源策略

- 无法读取非同源的 `Cookie、LocalStorage IndexDB`等内容

- 无法读取和修改非同源的`DOM`

- 无法发送非同源的`AJAX`，如果发送了非同源请求就会被浏览器拦截
- 无法发送非同源的请求

### 3、为什么需要同源策略

如果没有同源策略，那么假如小明在A网站登入了，如果小明在没有注销期间访问了B网站，B网站中携带了js脚本向A网站发起了模拟用户的请求，那么此请求会携带上浏览器保存的Cookie，这样B网站的js脚本就可以模拟小明随意操作了

### 4、跨域请求

在前后端分离的应用中需要规避这个同源策略，因为这是我们主动发起的跨域请求，自认为是安全的，例如前端地址为`abc.com:8080`,后端API为`abc.com:9090`。直接访问API会触发同源策略，所以需要想办法跨过去

主要有如下几个跨域请求的方法:

- **CORS 跨域资源共享**  使用专用的HTTP头，后端API服务器`abc.com:9090`告诉浏览器特定URL `abc.com:8080`的ajax请求可以直接使用，不会激活同源策略

- **nginx反向代理** 前端通过指定路径 `abc.com:8080/api` 访问后端请求，nginx配置此前缀的URL反向代理到`abc.com:8080`

-  所有拥有`src`属性的 `<script>、<img>、<iframe>` 以及`<a>`标签等，是不会经过同源策略，因为这些标签肯定是网站主动引用外部资源的所以不需要规定同源策略，例如`baidu.com`引用了CDN的`jquery`

    所以我通过调用js脚本的方式，从服务器上获取JSON数据绕过同源策略

    此规则会引发 **CSRF攻击**
    
- **JSONP** 方式，使用回调函数来获取数据，也是因为所有`src`都可以进行跨域

### 5、CORS跨域资源共享

浏览器通过 Header 响应请求头告诉浏览器允许哪些URL、哪些请求方法允许跨域请求，这样浏览器就允许指定URL进行跨域请求了

跨域过程如下:

- 客户端进行跨域之前都会发送一个 **GET或则OPTIONS** 请求通知服务器 “我要跨域了，告诉我允许跨域那些访问” 
- OPTIONS或则GET请求会带上一些客户端信息，比如`Origin`  然后服务器会进行检查和权限严重等操作
- 如果服务器检查通过则设置允许跨域相关头部告诉浏览器允许跨域

下面请看一些跨域相关的请求头

`Origin` 客户端告诉服务器该请求来自哪个域，服务器通过这个字段进行校验检查客户端是否合法，如果合法则设置 `Access-x`相关头部通知浏览器允许跨域，不合法则拒绝跨域

```bash
Origin: abc.com:8080
```

`Access-x` 允许跨域的相关请求头

```bash
#1、服务器告诉浏览器那些域允许范围 *表示所有域都可
Access-Control-Allow-Origin: abc.com:8080
#2、允许那些请求方法跨域
Access-Control-Allow-Methods: GET,POST,OPTIONS
#3、请求允许携带的头部
Access-Control-Allow-Headers: Content-Type
#4、告知浏览器哪些响应头部可以供客户端使用认情况下只有 Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma 可供使用
Access-Control-Expose-Headers: Content-Encoding,Conent-Length
#5、告知浏览器该响应的信息可以缓存多久
Access-Control-Max-Age:1800
#6、告知浏览器是否可以将 Credentials 暴露给客户端使用，Credentials 包含 cookie、authorization 类头部、TLS证书等
Access-Control-Allow-Credentials:true
```

下面是设置跨域的标准代码

```go
// 处理跨域请求,支持options访问
func Cors() gin.HandlerFunc {
	return func(c *gin.Context) {
		method := c.Request.Method
		origin := c.Request.Header.Get("Origin")
		c.Header("Access-Control-Allow-Origin", origin)
		c.Header("Access-Control-Allow-Headers", "Content-Type,AccessToken,X-CSRF-Token, Authorization, Token,X-Token,X-User-Id")
		c.Header("Access-Control-Allow-Methods", "POST, GET, OPTIONS,DELETE,PUT")
		c.Header("Access-Control-Expose-Headers", "Content-Length, Access-Control-Allow-Origin, Access-Control-Allow-Headers, Content-Type")
		c.Header("Access-Control-Allow-Credentials", "true")

		// 放行所有OPTIONS方法
		if method == "OPTIONS" {
			c.AbortWithStatus(http.StatusNoContent)
		}
		// 处理请求
		c.Next()
	}
}
```

### 6、JSONP跨域

原理就是定义一个`js`函数，然后用`src`引入的方式请求不同域的js资源，然后服务器就会返回一个 **函数调用的字符串，包括转入的json参数** ，浏览器会直接解析这个字符串为js代码进行调用，这样就可以间接获取到服务区传入的json数据了

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<script>
    function hello(data){
        console.log(data);
    }
</script>
<script src="http:localhost:8080/js?callback=hello"></script>
<body>
</body>
</html>
```

远程服务器

```go
func main() {
   r := gin.Default()

   r.GET("/js", func(context *gin.Context) {
      callback := context.Query("callback") //获取约定的回调函数名
      data := map[string]string{
         "name": "lyer",
         "age":  "19",
      }
      jsonData, _ := json.Marshal(data)
      context.String(200, fmt.Sprintf("%s(%s)", callback, jsonData))
      //context.JSONP(200, data)
   })

   r.Run(":8080")
}    
```

​    

## XSS和CSRF攻击

### XSS 

**Cross Site Script 跨站脚本攻击**

简单来说就是恶意的用户通过输入框、表单等输入恶意的js代码然后对原来的网站造成修改或则背后运行恶意js脚本，以至于其他用户访问该网站的时候都会执行此段代码

### CSRF

**Cross Site Request Forgery 跨站请求伪造攻击**

所有拥有`src`属性的 `<script>、<img>、<iframe>` 以及`<a>`标签等，是不会经过同源策略，因为浏览器认为这些标签肯定是网站主动引用外部资源所加入的的所以不需要规定同源策略，例如`baidu.com`引用了CDN的`jquery`

浏览器为了方便表单的提交，所以 `<form>` 中填的`action` 也不会限制同源策略，可以填写任意的URL，这样就会造成 **CSRF攻击**  攻击站点可以诱导用户点击然后就可以利用浏览器保存的A站点的Cookie发起访问，提交表单数据等

规避CSRF攻击的方法如下:

- 服务器检查 `Referer` 头部标签，检查是否是自己的站点发来的请求
- 表单隐藏一个唯一有时效性的 **Token** 字段，这样就无法伪造表单了，服务器会检查每次表单提交时的 **Token** 是否有效

​    

## HTTP安全之Basic认证

Basic认证安全性比较低，但是其实现比较简单，只需要加一个头部即可

比如现在访问 `/api`资源，服务器发现没有通过认证，于是范围如下报文

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Basic realm=<认证范围,随便写>
```

现在浏览器都实现了Basic认证弹窗，当收到这样的报文之后浏览器就会弹窗要求用户输入密码，如果严重通过则可以访问，如果不通过则继续返回`401`或则返回`403`

用户输入密码之后，浏览器会在头部加上`Authorization: base64编码(username:pwd)`

​    

## 参考

- https://www.cnblogs.com/lightsong/p/4172979.html 【第三方Cookie】
- https://www.bwangel.me/2018/11/01/http-chunked 【Chunked分块传输】
- https://github.com/mojocn/flash/blob/master/main.go 【断点续传】
- https://www.zoo.team/article/http-cache 【缓存】
