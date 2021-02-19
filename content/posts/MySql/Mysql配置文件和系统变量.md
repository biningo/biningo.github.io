---
title: MySql配置文件
date: 2021-02-16
categories: [Mysql]
tags: [数据库,MySql] 
---

> **更新中......**

## 配置文件

MySql配置文件的作用就是给`mysqld`服务器进程提供启动参数，设置一些变量，也可以通过命令行方式设置参数，但是这样太麻烦了而且不是永久的，所以一般都将这些参数写在配置文件中，`mysqld`进程启动的时候会按照如下方式去寻找配置文件:

```bash
$ mysql --help | grep my.cnf
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf 
```

- `/etc/my.cnf`
- `/etc/mysql/my.cnf`
- `$MYSQL_HOME/my.cnf`
- `~/.my.cnf`

可以指定配置文件路径:

```bash
$ mysqld --defaults-file=/etc/my.cnf #只读取这一个配置文件
$ mysqld --defaults-extra-file=/my.cnf #添加额外的配置文件路径加入默认的搜索路径，也就是说其他路径包括这个也会被搜索到
```

**另外注意：命令行参数优先级高于配置文件，搜索路径前面的配置高于后面的，并且有些命令行参数是只属于命令行的，不属于配置文件，配置文件都可以用命令行替代**

配置文件分为如下几组:

```toml
[server]
...
[mysqld]
...
[mysqld_safe]
[client]
[mysql]
[mysqladmin]
```

​    

## [mysqld]配置

TODO

## [server]配置

TODO

## [client]配置

TODO

## [mysql]配置

TODO

## [mysqld_safe]配置

TODO

## [mysqladmin]配置

TODO

## 系统变量

### 1、系统变量的查看

系统变量一般通过`SHOW VARIABLES LIKE xxx`来查看，下面展示常用的系统变量

```mysql
SHOW VARIABLES LIKE 'default_storage_engine' #默认使用的搜索引擎
```

在配置文件的变量可以用`-`线链接，而这些变量保存在数据库中则是以`_`链接

### 2、系统变量作用范围

系统变量有作用的范围之分，有下面几个范围:

- `GLOBAL` 全局所有用户
- `SESSION` 特指当前一个用户，一般客户端可以通过连接字符串添加参数来设置这些变量比如设置 **时区、字符集** 等，这属于单个连接的客户端

查询的时候可以指定范围，不指定则默认是`SESSION`

```mysql
SHOW GLOBAL VARIABLES LIKE 'default_%'
```

### 3、系统变量的修改

修改系统变量可以在 **配置文件中修改**，也可以直接在数据库中修改，使用`SET` 指令，如果不指定范围，则默认也是`SESSION`

```mysql
#1、SET [GLOBAL|SESSION] 系统变量名 = 值;
SET default_storage_engine = MyISAM #默认SESSION作用域
SET GLOBAL default_storage_engine=InnoDB

#2、SET [@@(GLOBAL|SESSION).]var_name = XXX
SET @@GLOBAL.default_storage_engine = MyISAM;
SET @@default_storage_engine = MyISAM; #默认就是SESSION
```

### 4、系统变量注意点

- 大部分系统变量有很多都是可以在配置文件中指定的，有些系统变量是 **运行时生成** 的不可以当启动项来设置，比如: **状态变量** 

- 有些系统变量可以热跟新，修改即生效，有些则修改之后需要重启才会生效
- 有些系统变量只有`GLOBAL`或`SESSION`作用域，有些两种都可以设置
- 有的系统变量是 **readonly** 的，比如`version` MySql的版本

​    

## 状态变量

还有一种 **状态系统变量**，由系统运行时产生，用于记录服务器相关状态的，比如当前正在连接的客户端个数等

**状态变量** 也有两个范围:

- `GLOBAL`
- `SESSION`

```mysql
SHOW STATUS LIKE 'threads_connected' #当前有多少客户端连接
```

​    

## 常见系统变量

系统变量

```mysql
SHOW ENGINES #系统支持的存储引擎
SHOW CHARACTER SET #支持的字符集
```

```mysql
SHOW VARIABLES LIKE 'default_storage_engine' #默认使用的搜索引擎
SHOW VARIABLES LIKE 'max_connections' #运行的同时连接数
SHOW VARIABLES LIKE 'time_zone'#数据库使用的时区,默认为SYSTEM和操作系统保持一致
```

状态变量

```mysql
SHOW STATUS LIKE 'thread%' #注意Mysql的查询都是是不分大小写的
SHOW STATUS LIKE 'threads_connected' #当前有多少客户端连接
```

​    

## 参考

《MySql是怎样运行的》