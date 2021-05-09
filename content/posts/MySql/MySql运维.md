---
title: MySql运维
date: 2021-05-09
categories: [Mysql]
tags: [数据库,MySql] 
---

## MySql数据目录初始化

第一次启动MySql的时候需要进行初始化，也就是初始化数据存放的目录（**注意这个目录必须为空，或则不存在**）这个目录路径可以在配置文件里面配置

```bash
[mysqld]
port=3306
datadir=/home/lyer/tmp/data
```

默认的数据存放路径是:

```bash
$MYSQL_HOME/data
```

MySql服务器启动会读取配置文件`my.cnf`，配置文件查找顺序为:

```bash
/etc/my.cnf
/etc/mysql/my.cnf
$MYSQL_HOME/my.cnf
--default-extra-file #命令行自定配置文件路径
~/.my.cnf
```

服务器会依次查找这些路径去读取并且合并配置

下面是数据库初始化命令

```bash
mysqld --initialize #初始化 会打印初始的root密码在终端 第一次登入需要密码
mysqld --initialize-insecure #初始化并且设置root密码为空 也就是初次不需要密码
```

初始化之后我们需要登入到数据库，并且需要修改root密码为自定义的密码

```bash
mysql -uroot -p #没有密码则回车即可 有初始密码则需要输入
alter user 'root'@'localhost' identified by '55555';
```

如果觉得每次输入用户密码指定host麻烦的话则可以在`my.conf`里面配置客户端

```bash
[client]
host=127.0.0.1
user=root
password=55555
```

这样的话直接输入`mysql`就会读取客户端配置，这样就不需要输入密码了

​    

## MySql服务启动、停止

一般通过`$MYSQL_HOME/support-files/mysql.server`服务启动脚本启动，此脚本还会启动一个监控进程`mysqld-safe`，此进程会监控mysql服务的运行状态，如果出错了则会将错误日志记录起来`xxx.err`，还会帮助mysql服务重启

```bash
$MYSQL_HOME/support-files/mysql.server #不带参数执行可以查看Useage
mysql.server start #启动 (第一次启动服务器需要首先初始化数据库)
mysql.server status #查看mysql服务状态
mysql.server stop
```

​    

## 用户管理

和用户相关的信息都保存在`mysql.user`表中，密码都是加密存储的

```sql
select host,user from user;
```

`User`列表示用户名，`Host`列表示允许连接的客户端主机地址，为`localhost`则表示只允许本地连接

**创建一个用户**

```sql
create user lyer identified by '66666'; --不指定host的话默认全部可以连
create user 'lyer'@'%' identified by '55555'; --%表示允许全部IP连接
create user 'lyer'@'112.23.21.1' identified by '55555'; --指定IP
create user 'lyer'@'112.23.21.%' identified by '55555'; --指定IP网段可连
```

**修改用户密码**

```sql
alter user 'root'@'localhost' identified with mysql_native_password BY '77777';  
```

**修改用户可连接的**IP

```sql
update user set host='localhost' where user='lyer';
```

**删除用户**

```sql
drop user 'lyer'@'localhost';
```

查看当前登入用户

```sql    
select user();
```

​    

## 用户权限

权限类型

| **类型**       | **描述**         |
| -------------- | ---------------- |
| ALL            | 所有可用的权限   |
| CREATE         | 创建库、表和索引 |
| LOCK TABLES    | 锁定表           |
| ALTER          | 修改表           |
| DELETE         | 删除表           |
| INSERT         | 插入表或列       |
| UPDATE         | 更新表           |
| SELECT         | 检索表或列的数据 |
| CREATE_VIEW    | 创建视图         |
| SHOW DATABASES | 列出数据库       |
| DROP           | 删除库、表和视图 |

**授权**

```sql
grant <privileges> on <database>.<table> to 'username'@'localhost' WITH GRANT OPTION; --with grant option 用户表示可以将自己拥有的权限授予别人
```

```sql
grant select,delete on test.* to 'lyer'@'%'; --test.* 表示允许对test数据库中所有表进行select和delete
grant show databases on *.*  to lyer@'%';
```

**查看某个用户的权限**

```sql
show grants for lyer@'%';
```

**回收权限**

```sql
revoke <privileges> on <database>.<table> from 'username'@'localhost';
```

```sql
revoke show databases on *.* from lyer@'%';
revoke select,delete on test.* from lyer@'%';
```

​    

## 参考

[https://www.yuque.com/yinjianwei](https://www.yuque.com/yinjianwei/vyrvkf/lbd1qq)

