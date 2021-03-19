---
title: Shell脚本基本语法总结
date: 2021-03-19
categories: [Linux]
tags: [Linux,Shell]
---

## 变量

变量替换

```bash

```

**`${@}`和`${*}` 的区别**

两个都是用于获取所有入参，区别在于`"${@}"`被双引号包裹会展开里面的所有的值，而`"${*}"`则会将所有值当做一个，如果不加双引号那么他们就没有区别

```bash
#下面只会执行一次循环
for i in "${*}";do
   echo $i    
done

#下面会输出每个参数
for i in "${@}";do
   echo $i    
done
```

## 字符串

`''`和 `""` 的区别

```bash
name="lyer"
s1='hello,${name}\n' #不会转义,原样输出
s2="hello,${name}\n" #转义
```

字符串拼接

```bash
s1="abc"
s2="def"
s3=${s1}"-"${s2}
```

获取字符串的长度

```bash
name="lyer"
echo ${#name} #和array的操作一样
```

获取子串

```bash
${username:1} #[1,]
${username:1:2} #1开始获取2个 包括1
${username:1:-2} #[1,-2)
${username::2} #截取0-2 
${username: 2}
${username: -3}：提取最后3个字符，注意冒号后面添加一个空格：txt
```









## 参考

- https://wangdoc.com/bash/intro.html 【阮一峰Bash脚本教程】