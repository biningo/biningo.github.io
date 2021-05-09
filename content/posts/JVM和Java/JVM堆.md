---
title: JVM堆
date: 2021-05-08
categories: [JVM和Java]
tags: [JVM]
draft: true
---

## StackOverflowError

当一个线程的线程栈超过了最大大小则会引发此错误

​    

## OutOfMemoryError

当JVM发现堆中没有足够的空间分配给新对象时，会先进行一次`Full GC`，如果GC后还是空间不足，再抛出异常

​    

## 相关命令

调节堆的大小

```bash
java -Xms600m -Xmx700m   temp.Hello #temp/Hello.class
```

查看JVM堆内存分配

```bash
jps #获取到pid
jstat -gc 223848
```

调节线程栈的最大大小

```bash
-Xss100m
```

