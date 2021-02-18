---
title: SQL总结
date: 2021-02-16
categories: [Mysql]
tags: [数据库,MySql] 
---

## 表操作

建表

```mysql
CREATE TABLE test
(
) ENGINE = InnoDB #设置表的存储引擎
  DEFAULT CHARSET = utf8 #设置表的字符集、
  COLLATE=utf8_bin #设置字符集比较规则
```

```mysql
drop table if exists category,article,tag,article_tag;
CREATE TABLE IF NOT EXISTS category
(
    id    INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(50) NOT NULL,
    UNIQUE uq_title (title)
) ENGINE = InnoDB
  CHARACTER SET = utf8;

CREATE TABLE article
(
    id         INT AUTO_INCREMENT,
    title      VARCHAR(100) NOT NULL,
    created_at DATETIME     NOT NULL,
    updated_at DATETIME     NOT NULL,
    deleted_at DATETIME DEFAULT NULL,
    author     VARCHAR(20)  NOT NULL,
    status     ENUM ('published','auditing','draft','deleted') NOT NULL,
    cid        INT          NOT NULL,

    PRIMARY KEY pk_id (id),
    FOREIGN KEY fk_cid (cid) REFERENCES category (id) ON DELETE CASCADE,
    UNIQUE uq_title (title) COMMENT '会自动创建唯一索引'
) ENGINE = InnoDB
  CHARACTER SET = utf8mb4;

CREATE TABLE tag
(
    id    INT PRIMARY KEY AUTO_INCREMENT,
    title varchar(20) NOT NULL,
    UNIQUE uq_title (title)
);

CREATE TABLE article_tag
(
    id  INT PRIMARY KEY AUTO_INCREMENT,
    tid INT NOT NULL,
    aid INT NOT NULL,
    FOREIGN KEY fk_tid (tid) REFERENCES tag (id),
    FOREIGN KEY fk_aid (aid) REFERENCES article (id),
    UNIQUE uq_tid_aid (tid, aid)
);
```

展示建表语句

```mysql
SHOW CREATE TABLE test #展示test表的建表语句
DESC test
```

修改表

```mysql
ALTER TABLE test ENGINE=MyISAM
```

​    

## 系统变量

### 1、系统变量的查看

系统变量一般通过`SHOW VARIABLES LIKE xxx`来查看，下面展示常用的系统变量

```mysql
SHOW ENGINES #系统支持的存储引擎
SHOW CHARACTER SET #支持的字符集
SHOW VARIABLES LIKE 'default_storage_engine' #默认使用的搜索引擎
SHOW VARIABLES LIKE 'max_connections' #运行的同时连接数
SHOW VARIABLES LIKE 'time_zone' #数据库使用的时区 默认为SYSTEM和操作系统保持一致

```

在配置文件的变量可以用`-`线链接，而这些变量保存在数据库中则是以`_`链接

系统变量有作用的范围之分，有下面几个范围:

- `GLOBAL` 全局所有用户
- `SESSION` 特指当前一个用户，一般客户端可以通过连接字符串添加参数来设置这些变量比如设置 **时区、字符集** 等，这属于单个连接的客户端

查询的时候可以指定范围，不指定则默认是`SESSION`

```mysql
SHOW GLOBAL VARIABLES LIKE 'default_%'
```

还有一种 **状态系统变量**，由系统运行时产生，用于记录服务器相关状态的，比如当前正在连接的客户端个数等

```mysql
SHOW STATUS LIKE 'thread%' #注意Mysql的查询都是是不分大小写的
SHOW STATUS LIKE 'Threads_connected' #当前有多少客户端连接
```

### 2、系统变量的修改

修改系统变量可以在配置文件中修改，也可以直接在数据库中修改，使用`SET` ，如果不指定范围，则默认也是`SESSION`

```mysql
#1、SET [GLOBAL|SESSION] 系统变量名 = 值;
SET default_storage_engine = MyISAM
SET SESSION default_storage_engine=InnoDB

#2、SET [@@(GLOBAL|SESSION).]var_name = XXX
SET @@SESSION.default_storage_engine = MyISAM;
SET @@default_storage_engine = MyISAM; #默认就是SESSION
```

### 3、系统变量注意点

- 大部分系统变量有很多都是可以在配置文件中指定的

    有些系统变量是 **运行时生成** 的不可以当启动项来设置，比如: **状态变量** 

    并且有些系统变量可以热跟新，修改即生效，有些则修改之后需要重启才会生效

- 有些系统变量只有`GLOBAL`或`SESSION`作用域，有些两种都可以设置

- 有的系统变量是 **readonly** 的，比如`version` MySql的版本

- **状态变量** 也有两个范围`GLOBAL`和`SESSION`

​    

## 内置函数和变量

```mysql
#获取当前时间
SELECT NOW()
SELECT CURRENT_DATE
----------------------
```

​    

## 参考

《SQL学习指南(第二版修订版)》

《MySql是怎样运行的》

《MySql必知必会》

《高性能MySql(第三版)》



