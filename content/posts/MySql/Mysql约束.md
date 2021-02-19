---
title: MySql约束
date: 2021-02-19
categories: [Mysql]
tags: [数据库,MySql] 
---

## 约束分类

约束就是给字段设置一定的规则，**约束这个字段** 主要有如下几类约束

- 主键约束
- 外键约束
- 非空约束
- 唯一约束
- 默认值约束
- 检查约束

​    

## 主键约束

主键约束是非空的，最好使用 `AUTO_INCREMENT`来自动增加防止重复，并且能用`int`就不要用 `bigint` ，因为这和索引有关系，主键数据越小越好 **如果一张表没有设置主键，那么Mysql就会拿一个非空约束字段当做主键，如果都没有则会自己生成一个RowID的列作主键**

```mysql
CREATE TABLE tb(
	id int PRIMARY KEY AUTO_INCREMENT
)
```

```mysql
CREATE TABLE tb(
	id int AUTO_INCREMENT,
    name varchar(20),
    PRIMARY KEY pk(id)
)    
```

```mysql
#添加主键约束
ALTER TABLE  表名
ADD CONSTRAINT  约束名  PRIMARY KEY(列名);
#删除主键约束
ALTER TABLE  表名
DROP PRIMARY KEY 约束名;
```

​    

## 外键约束

外键是指引用另一个表中的一列或多列，**被引用的列应该具有主键约束或唯一约束**

外键约束可以设置联级: 

- **联级删除**: 外键表中被引用的记录删除之后则连同所有拥有这个外键的记录都删除
- **联级更新**:  外键表中被引用的记录更新之后则连同所有拥有这个外键的记录都更新

如果不设置则不允许删除还被引用的记录

```mysql
CREATE TABLE IF NOT EXISTS category
(
    id    INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(50) NOT NULL,
    UNIQUE uq_title (title)
);

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
    #联级删除  category记录删除了 所有引用该记录的article记录也一并删除
    #如果没设置此联级策略则不允许删除还被引用的category记录
    FOREIGN KEY fk_cid (cid) REFERENCES category (id) ON DELETE CASCADE,
    UNIQUE uq_title (title) COMMENT '会自动创建唯一索引'
);
```

```mysql
#添加外键
ALTER TABLE  表名
ADD CONSTRAINT  约束名  
FOREIGN KEY (外键字段名)  REFERENCES 外键表名(列名);
#删除外键
ALTER TABLE  表名
DROP FOREIGN KEY 外键名;
```

​    

## 非空约束

值不能为空，最好都把值设置为不为空，即使填个默认值上去也可

```mysql
CREATE TABLE tb(
	name varchar(10) NOT　NULL
)
```

```mysql
#设置非空约束
ALTER TABLE  表名
MODIFY  列名 该列数据类型 NOT NULL;

#删除非空约束
ALTER TABLE  表名
MODIFY  列名 该列数据类型;
```

​    

## 默认值约束

```mysql
CREATE TABLE tb(
	name varchar(10) DEFAULT 'aaa'
)
```

```mysql
#设置默认值约束
ALTER TABLE  表名
ALTER   列名  SET DEFAULT 默认值;
#删除默认值约束
ALTER TABLE  表名
ALTER   列名  DROP DEFAULT ;
```



## 唯一约束

表示该列的值没个记录都必须唯一，不允许重复，**唯一约束创建之后就会为该列创建一个索引，该索引就叫唯一索引** 创建唯一索引是为了每次插入的时候进行检查唯一性的时候能快点，随意创建唯一索引也就是给这个列添加了唯一约束 **所以唯一约束就是添加了一个索引**

```mysql
CREATE TABLE IF NOT EXISTS category
(
    id    INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(50) NOT NULL, #也可以直接写在行内
    UNIQUE uq_title (title) #单独写唯一约束
    #UNIQUE INDEX uq_title(title) 唯一索引写法 其实都一样
);
```

```mysql
#添加唯一约束
ALTER TABLE  表名
ADD CONSTRAINT  约束名 UNIQUE(列名);
#删除唯一约束索引
DROP INDEX  约束名  ON 表名
```

​    

## 参考

- https://juejin.cn/post/6844903955441385486#heading-2

