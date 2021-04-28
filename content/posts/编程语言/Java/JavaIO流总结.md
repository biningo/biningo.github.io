---
title: JavaIO流总结
date: 2021-03-23
categories: [编程语言/Java]
tags: [Java]
---

## OverView

Java 的 I/O 大概可以分成以下几类：

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 非阻塞IO：NIO

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/javaIO.png)

​      

## File文件相关操作

`File`类是操作文件系统的相关操作，如果要读取文件内容的数据，则需要传递给IO流对象，通过IO流的方式进行读取

```java
File f = new File("/home/pb/tmp/printf.sh");
FileInputStream inputStream = new FileInputStream(f);
```

通过操作此对象可以**创建文件、列出目录下的文件列表、判断文件是否可读、判断类型**等操作

```java
f.isFile();
f.createNewFile();
```

​        

## InputStream

**字节流接口**，所有流的接口，比如: 

- `FileInputStream`文件流用于读取文件
- `ByteArrayInputStream` 从字节数组里面读取流

OutPutStream就是将数据写入到目标处，这里将程序员作为主体，Out表示程序员将数据输出到计算机，Input则表示计算机的数据要输入到程序员中

​    

## BufferedInputStream

带有一个字节缓冲数组的`InputStream`  ，需要传入一个实现了 **InputStream** 接口的流，比如文件，**使用了装饰者模式** ，读取流的话不是直接操作文件了，而是每次读取会预先读取到一块`byte[]`中，相当于缓冲，然后更具用户读取的大小从缓冲区里面读取到用户的字节数组中

​    

## Reader

**字符流接口** 一个字符可能有多个字节组成比如汉字，实现的接口有:

- `InputStreamReader` 接受一个 **InputStream** 对象，然后按照编码将字节进行转化为字符
- `FileReader` 继承 **InputStreamReader** ，用于直接读取文件，里面就是根据传入的File对象或则文件名创建一个 **FileInputStream** 然后传入父类 InputStreamReader

​    

## BufferedReader

带缓冲的`Reader`，需要传入一个实现了 **Reader** 接口的流，比如文件`FileInputStreamReader`，读取的话会先读入到`char[]`中，然后直接从`char[]`缓冲区进行读取，**装饰者模式**

​    

## NIO

TODO

​    

## 参考

- https://blog.csdn.net/zhaoyanjun6/article/details/54292148 【BIO】