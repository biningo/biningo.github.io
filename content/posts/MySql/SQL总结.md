---
title: SQL总结
date: 2021-02-16
categories: [Mysql]
tags: [数据库,MySql] 
---

> **更新中......**

## 表操作

```mysql
CREATE TABLE test
(
) ENGINE = InnoDB #设置表的存储引擎
  DEFAULT CHARSET = utf8 #设置表的字符集、
  COLLATE=utf8_bin #设置字符集比较规则
```

```mysql
DROP TABLE IF EXISTS category,article,tag,article_tag; #删除表
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

```mysql
#展示建表语句
SHOW CREATE TABLE test #展示test表的建表语句
DESC test #展示字段信息
#修改表
ALTER TABLE test ENGINE=MyISAM #修改存储引擎
```

​        

## 索引

```mysql
#查看索引
SHOW INDEX FROM tb

#建表指定索引
CREATE TABLE tb(
    name varchar(200) NOT NULL ,
    index idx_name(name) #建表时就指定索引
)

#通过CREATE来创建索引
CREATE INDEX index_name ON table_name (column_list)
CREATE UNIQUE INDEX index_name ON table_name (column_list)
#通过ALTER来创建索引
ALTER TABLE table_name ADD INDEX index_name (column_list) #普通索引
ALTER TABLE table_name ADD UNIQUE (column_list) #唯一索引
ALTER TABLE table_name ADD PRIMARY KEY (column_list) #主键索引

#删除索引
DROP INDEX index_name ON talbe_name
ALTER TABLE table_name DROP INDEX index_name
ALTER TABLE table_name DROP PRIMARY KEY #删除主键可以不指定索引名字 因为主键只有一个
```

​    

## 约束

请参考 **Mysql约束** 这篇文章

​    

## 事务

```mysql
SHOW VARIABLES LIKE '%AUTOCOMMIT%';
SET AUTOCOMMIT = 0; #取消事务自动提交 on|off
begin;
commit;
rollback;
```

​    

## explain

查看查询语句相信信息

​    

## 内置函数

TODO

## 参考

《SQL学习指南(第二版修订版)》

《MySql是怎样运行的》

《MySql必知必会》

《高性能MySql(第三版)》



