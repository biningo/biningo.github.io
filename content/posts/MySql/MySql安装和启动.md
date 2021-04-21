---
title: MySql安装和启动
date: 2021-04-18
categories: [Mysql]
tags: [数据库,MySql] 
---

## Linux安装

Linux下可以直接下载tar的二进制文件，在官网下载即可

下载之后文件里面有如下几个内容:

```bash
bin  docs  include  lib  LICENSE  man  README  share  support-files
```

我们需要设置一个`MYSQL_HOME` 环境变量指向这个地址

​    

## 初始化数据目录

MySql第一次启动需要初始化数据目录，数据目录的位置默认不指定的话则为MySql安装目录下的`data` (这个和环境变量无关，不设置MySql家目录的环境变量也会到这个路径下找，这个是因为Mysql二进制包已经编译好了就是这个目录)当然也可以自己拿源码编译为其他路径

初始化数据目录需要执行一下命令:

```bash
mysqld --initialize  --user=root #--user=root表示以root用户执行，mysql官方不建议以root执行mysql，如果需要强制执行则需要指定--user=root
```

初始化之后就会生成一个`data`目录，此目录就和存储有关了，所有的数据都存储在此目录下

然后就可以执行如下命令启动`mysqld`进程了

> 初始的用户密码在初始化时会打印在屏幕下

```bash
mysqld  --user=root
```

首次用客户端连接时，系统会要求你必须修改root密码

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123';
```

