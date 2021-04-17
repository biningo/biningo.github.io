---
title: 常见的Hash算法
date: 2021-04-17
categories: [网络安全]
tags: [Hash,加密]
---

## 什么是Hash算法

Hash表这种数据结构中就是使用了Hash算法，其将值进行Hash转化为表数组对应的下标，这种Hash算法的目的就是将其他数据比如 **字符串、int、bool** 转化为数组下标(正整数)

下面来看看`JDK11`里面`HashMap`的`hash`函数

```java
static final int hash(Object key) {
    int h;
    return key == null ? 0 : (h = key.hashCode()) ^ h >>> 16;
}
```

主要通过key的`hashCode`函数来计算hash值，并且还进行了二次hash来减少碰撞

用户实现的这个`hashCode`有如下几个规定:

- 如果 key1 = key2，那 hash(key1) == hash(key2)
- 如果 key1 != key2，那 hash(key1) != hash(key2) 如果不同的key产生了相同的hash值那么就代表产生了hash碰撞
- `hashCode`必须返回一个非负整数

我们再来看一看`String`的`hashCode`的实现，下面是英文的拉丁字母的hashCode函数

至于为什么`31`这是为什么减少hash碰撞，因为`31`是一个不大不小的质数

```java
public static int hashCode(byte[] value) {
    int h = 0;
    byte[] var2 = value;
    int var3 = value.length;
    for(int var4 = 0; var4 < var3; ++var4) {
      byte v = var2[var4];
      h = 31 * h + (v & 255); //字母会转化为对应的ASCII码值然后计算hash
    }
    return h;
  }
```

> Java中`Integer`类型的hashCode是它本身

上面的是哈希表数据结构使用的hash算法，还有一类hash算法是用于信息加密、信息摘要的hash算法，就是输入一段不定长的文本或则其他数据到hash函数，然后输出 **固定位数的二进制串** 也就是`hash值` 

此类常见的hash算法有如下几种:

- **MD5(128位)**
- **SHA256(256位)**

​     

## MD5算法

MD5算法无论输入多长最终会输出`128位`的二进制串，通常使用 **16进制** 表示，如下:

```bash
md5sum a.txt
#每一位都是一个16进制的值，这里一共32位16进制  所以 32*4=128
757228086dc1e621e37bed30e0b73e17
```

MD5现在已经证实不安全，会产生hash碰撞，所以不能用于加密领域，一般用于生成文件的摘要、生成数据的摘要这种场景来检验文件和数据是否被篡改

​    

## SHA256算法

全名: `Secure Hash Algorithm 256` 

SHA256算法无论输入多长最终会输出`256位`的二进制串，使用16进制表示出来

```bash
sha256sum a.txt
#可以看出这是上面的两倍长度
f7626dcb23bbdf3a84360bbd2d5b9afb43d9a1e09c29aa53c386ac6d89b1e45e
```

SHA256现在还是比较安全的，广泛用于加密领域，并且广泛用于 **比特币挖矿和加密**

​    

## 参考

[一文读懂 MD5 算法](https://segmentfault.com/a/1190000021691476)