---
title: MySql备份与恢复
date: 2021-05-09
categories: [MySQL]
tags: [数据库,MySql,备份] 
---

## mysqldump备份与恢复

备份所有数据库`--all-databases`

```bash
#备份所有数据库
mysqldump -uroot -p --all-databases > ./all.sql
```

备份指定的数据库

```bash
#备份 lyer test 库
mysqldump -uroot -p lyer --databases lyer test   > ./db.sql
```

备份指定数据库中的表，恢复时必须use到指定的数据库下

```bash
#备份test库中的a b c 表
mysqldump -uroot -p test a b c   > ./db.sql
```

恢复数据

```bash
mysql> source /home/lyer/tmp/temp/db.sql
```

​    

## MySql备份脚本

```bash
#!/bin/bash
# -------------------------------------------------------------------------------
# FileName:    mysql_backup.sh 
# Describe:    Used for database backup
# Revision:    1.0
# Date:        2020/08/11
# Author:      wang

# 设置mysql的登录用户名和密码(根据实际情况填写)
mysql_user = "root"
mysql_password = "yourpassword"
mysql_host = "localhost"
mysql_port = "3306"
backup_dir = /data/mysql_backup

dt=date +'%Y%m%d_%H%M'
echo "Backup Begin Date:" $(date +"%Y-%m-%d %H:%M:%S")

# 备份全部数据库
mysqldump -h$mysql_host -P$mysql_port -u$mysql_user -p$mysql_password -R -E --all-databases --single-transaction > $backup_dir/mysql_backup_$dt.sql

find $backup_dir -mtime +7 -type f -name '*.sql' -exec rm -rf {} \;
echo "Backup Succeed Date:" $(date +"%Y-%m-%d %H:%M:%S")
```

​    

## binlog恢复

`binlog`属于`server`层面的，所有存储引擎共享的日志，和存储引擎无关

> innodb觉得此日志不可靠并且效率不高，于是实现了自己的`redo`日志来保证事务持久化。`binlog`记录的是数据的逻辑处理过程，而`redo`日志记录的是数据对应的物理修改，比如数据页的哪个位置做了什么修改。所以`redo`日志恢复效率比`binlog`恢复效率更高。但是`redo`日志不可用于备份恢复，`redo`日志是文件的大小是固定的(大小可以配置)，采用循环写的方式，而`binlog`大小是无限追加的
>
> `redo`日志认为只要数据页持久化到磁盘之后则此日志就无效了，但是如果磁盘损坏这种情况就无法避免了，这个时候就可以用`binlog`来恢复数据

一般通过备份恢复的数据还是不全的，全量备份之后的数据可以通过`binlog`来恢复，`binlog`的作用有以下几个:

- 主从同步（`master`发送自己的`binlog`到从机，从机通过此日志进行同步）
- 数据恢复（使用`mysqlbinlog`工具来恢复数据）

从指定时间点和结束时间点开始，恢复这段时间内的数据

```bash
mysqlbinlog --start-datetime="2021-05-09 22:34:00" --stop-datetime="2021-05-09 22:36:00" --database=test binlog.000001 | mysql -uroot -p
```

​      

## 参考

[MySQL备份脚本，应该这么写](https://juejin.cn/post/6860665123796287501)

[mysql数据恢复，binlog详解](https://juejin.cn/post/6844903897249628174#heading-4)