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

## 常见系统变量



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



