---
title: Base64编码
date: 2021-03-02
categories: [网络安全]
tags: [编码,网络]
---

## 什么是Base64编码

将二进制数据转化为`64`个可打印`ASCII码`的一种编码方式，这`64`个可打印字符分别为:

```bash
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=
```

注意，最后一个`=`不算，`=`当做`Base64`编码的结尾标志

> 总结：**基于 64 个可打印字符来表示二进制数据的表示方法**

​    

## Base64编码原理

- 将待转换的字符串每3个字节分为一组，每个字节占 8 个二进制位，那么共有24个二进制位
- 将第（1）步得到的每 24 个二进制位分为每 6 个一组（因为`2^6=64`），则**每 3 个字节可分为 4 组**
- 在每组前面添加两个 0 ，补齐1字节，每组由 6 个二进制位变为 8 个二进制位，总共 32 个二进制位，即四个字节 （**3字节变4字节，所以经过Base64编码的都会变大**）

- 6个二进制位转化为十进制然后检索下表，根据 **Base64编码对照表**（见下表）获得对应的值

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/base64.jpg)

​     

## 为什么么需要Base64编码

- **方便网络传输**  Base64的那`64`个字符只包含最基础的字符，所以在各种环境进行传输的时候，极大地减少了出现莫名其妙的问题的概率
- **简单的隐藏可见内容** （`Base64`只是一种编码方式，不能用来加密信息）
- **将二进制数据转化为可打印字符**

​    

## Base64优缺点

缺点:

- Base64会增加字节数
- Base64编码不是加密算法

优点:

- Base64能简单隐藏内容
- Base64算法简单可逆，不会消耗太多CPU，不影响效率
- Base64能将所有数据转化为可打印数据
- 减少网络传输出现的编解码错误

​    

## Base64的使用场景

- 网上留言自己的联系方式等私密信息，可以使用Base64加密一下，这样就不会直接泄露自己的信息，只有真正需要的人才会拿去解密，可以过滤一部分人
- 包含一些特殊字符的字符串等可以先用Base64编码一下防止被转义
- http请求，在解析参数时是根据?和=号去解析的。那么当key value中包含=号等特殊字符时，参数解析就会错误，可以使用Base64编码解决
- 网页中一些小图片小文件等可以直接以base64编码的方式嵌入，不用再用链接请求消耗资源(**HTML不支持直接二进制格式嵌入**)
- 很多比较老的协议还是只支持纯文本的,比如`SMTP`协议，如果需要在这些协议上传输其他字符则需要使用Base64编码

​    

## 编程实现

下面用`Go`进行编码解码Base64，注意下面是标准的Base64编解码，还有URL的Base64编解码

```go
func main() {
	str := []byte("hello,world")
	dst:=base64.StdEncoding.EncodeToString(str)
	log.Println(dst)
	str ,_= base64.StdEncoding.DecodeString(dst)
	log.Println(string(str))
}
```

​    

## 参考

https://www.cxyxiaowu.com/535.html

https://www.zhihu.com/question/36306744

https://blog.csdn.net/weixin_40117614/article/details/90670159