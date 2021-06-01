---
title: HTTP范围请求
date: 2021-05-31
categories: [网络/HTTP]
tags: [HTTP]
draft: true
---

## Chunk传输

每个HTTP响应报文都必须在Header中指定`Content-Length`，此长度就是`Body`的长度，浏览器会根据这个头部去解析读取Body的数据，如果这个长度小于Body，那么就无法完全读取Body的数据，如果大于Body的数据那么就会报错

如果不指定`Content-Length`那么，则必须指定另外一个头部

```http
Transfer-Coding:chunked
```

在遇到结束chunk传输的结束标记之前浏览器就会一直处于`Pending`状态等待服务器传输分块的数据，直到遇到结束标记则会将之前发送的所有TCP包拼装为一个HTTP报文当响应的报文Body长度是不确定的，比如数据是动态生成的无法计算出来，那么也可以使用Chunk传输，有了`Transfer-Coding:chunked` 之后浏览器就会忽略`Content-Length`头部，这两个头部只能有一个出现在Header中

分块传输的结构如下:

<img src="https://raw.githubusercontent.com/biningo/cdn/master/img1/chunked.png" style="zoom:70%;" />

下面用Go来模拟一下Chunked传输

```go
func chunkedHandler(conn net.Conn) {
	conn.Write([]byte("HTTP/1.1 200 OK\r\n"))
	conn.Write([]byte("Transfer-Encoding: chunked\r\n"))
	conn.Write([]byte("\r\n"))
    
	//数据太大则会被分为多个TCP包传输，下面传输14*500字节的数据
	for i := 0; i < 500; i++ {
		conn.Write([]byte("6\r\n"))
		conn.Write([]byte("hello,\r\n"))
		conn.Write([]byte("8\r\n"))
		conn.Write([]byte("chunked!\r\n"))
	}
	//结束标记
	conn.Write([]byte("0\r\n"))
	conn.Write([]byte("\r\n"))
}
```

```go
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

HTTP的范围请求可以将一个数据分为好几段来请求，或则只请求数据中的某段数据

比如视频传输中如果用户拉动进度条跳到视频的中间部分，那么就只需要将视频中间部分传输给客户端即可而不需要将整个视频都传过去

在大文件下载中如果网络断开或则其他原因导致下载失败，如果使用分段下载的话则每次失败就不需要将全部的文件再从头下载了，只需要将失败的部分文件重新下载即可

范围请求需要我们自己在服务器和客户端实现，需要设置几个请求头，然后实现相关的处理逻辑 (因为HTTP只是规定的协议，而协议的实现则需要server和client共同去完成)

`Accept-Range` 表示服务器是否支持范围请求

- `none`  或则没有这个响应头部则表示服务器不支持范围请求
- `bytes` 表示server支持范围请求

```bash
Accept-Range: none #不支持
Accept-Range: bytes #支持
```

`Range` 请求头部，代表客户端请求的字节范围，从0开始计数

如果body使用gzip压缩的话则此大小代表的是压缩前的文件大小，比如看视频拖拽则必须按原视频的大小进行范围请求

```bash
Range: bytes=0-10 #[0,10]一共11个字节
Range: bytes=10- #[10,] 10字节之后所有
Range: bytes=-500  #最后500字节
```

`Content-Range` 响应头部，表示服务器返回的数据的范围，范围请求的状态码为`206`

```bash
Content-Range: bytes 0-31/96 #96是数据的全部大小
```

允许一次请求多个片段，如果一次请求多个片段则必须指定`Content-Type`

```http
#boundary是多段数据的分割符
Content-Type:multipart/byteranges; boundary=00000000001
```

请求响应示例

```http
GET /16-2 HTTP/1.1
Host: www.xxx.com
Range: bytes=0-9,20-29
```

```http
HTTP/1.1 206 Partial Content
Content-Type: multipart/byteranges; boundary=###########
Connection: keep-alive
Accept-Ranges: bytes

--###########
Content-Type: text/plain
Content-Range: bytes 0-9/96

1234567890
--###########
Content-Type: text/plain
Content-Range: bytes 20-29/96

abcdefghij
--###########--
```

​    

## HTTP范围请求的应用

- 多线程并发下载大文件，最后将多个分片数据合并即可
- 断点下载(断点续传)，下载中断了可以只传输没有传输的那部分数据
- 音乐视频拖拽播放，拖到哪一部分则传输对应部分的文件即可

​    

## 多线程并发下载器实现思路

服务器实现

- 先发送`HEAD`请求查看服务器是否支持范围请求，如果服务器支持则返回`Accept-Range: bytes`
- client请求指定范围数据，如果超过了数据的范围，则服务器返回`416` 
- 如果条件符合，则服务器读取`Range`信息计算数据的偏移量读取读取文件片段并且在相应头部需要设置`Content-Range` 来标识这个数据属于哪一个范围

客户端实现

- 客户端发送一个`HEAD`请求查看服务器是否支持范围请求，并且获取数据的总体大小
- 开`N`个线程同时使用`Range`分段，然后并发下载
- 下载失败只需要重新下载失败的那一块数据即可，不需要全部再重新下载
- 下载完成之后只需要将每个分片合并为一个文件即可

​      

## Chunk传输和范围请求的区别

Chunk传输和范围请求名字上听起来是比较类似的，其实他们的区别很大

Chunk传输特点如下:

- HTTP响应只有一个HTTP报文，如果数据很大会被分为多个TCP报文，因为TCP是面相流的
- Body长度未知，一次HTTP请求传输的是整个资源
- 因为`Content-Length`未知并且数据是动态生成的，则需要用分割符来解析Body数据，否则浏览器不知道如何读取body数据

范围请求的特点如下:

- 每个范围请求对应一个HTTP响应报文
- 只请求资源的一部分数据
- 用`Content-Range`标识响应的是哪个范围的数据，用`Range`标识请求的是哪个范围的数据

​    

## 参考

[https://github.com/mojocn/flash](https://github.com/mojocn/flash)

[字节跳动面试官：请你实现一个大文件上传和断点续传](https://juejin.cn/post/6844904046436843527#heading-1)