---
title: MySql配置文件
date: 2021-02-16
categories: [Mysql]
tags: [数据库,MySql] 
---

MySql配置文件的作用就是给`mysqld`服务器进程提供启动参数，设置一些变量，也可以通过命令行方式设置参数，但是这样太麻烦了而且不是永久的，所以一般都将这些参数写在配置文件中，`mysqld`进程启动的时候会按照如下方式去寻找配置文件:

```bash
$ mysql --help | grep my.cnf
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf 
```

- `/etc/my.cnf`
- `/etc/mysql/my.cnf`
- `~/.my.cnf`

可以指定配置文件路径:

```bash
$ mysqld --defaults-file=/etc/my.cnf #只读取这一个配置文件
$ mysqld --defaults-extra-file=/my.cnf #添加额外的配置文件路径加入默认的搜索路径，也就是说其他路径包括这个也会被搜索到
```

**另外注意：命令行参数优先级高于配置文件，搜索路径前面的配置高于后面的，并且有些命令行参数是只属于命令行的，不属于配置文件，配置文件都可以用命令行替代**