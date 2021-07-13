---
title: MySql数据类型
date: 2021-05-25
categories: [Mysql]
tags: [MySql]
draft: true
---

## 数字类型

| 类型           | 字节大小 (byte) |
| -------------- | --------------- |
| `TINYINT`      | 1               |
| `SMALLINT`     | 2               |
| `MEDIUMINT`    | 3               |
| `INT`          | 4               |
| `BIGINT`       | 8               |
| `FLOAT`        | 4               |
| `DOUBLE`       | 8               |
| `DECIMAL(M,D)` | 依赖M,D         |

有些情况下可能在`tinyint`下会设置长度比如`tinyint(3)`，这对于tinyint没有任何影响，只是为了提示一些东西，比如`tinyint(1)`表示此字段存储的是类似于bool值，框架会根据这些提示来将tinyint值转化为bool值

​    

## 时间日期类型

| 类型        | 大小 ( bytes) | 格式                  |
| :---------- | :------------ | :-------------------- |
| `DATE`      | 3             | `YYYY-MM-DD`          |
| `TIME`      | 3             | `HH:MM:SS`            |
| `DATETIME`  | 8             | `YYYY-MM-DD HH:MM:SS` |
| `TIMESTAMP` | 4             | 时间戳                |

​    

## 字符串类型

| 类型       | 大小 (byte)          | 用途                            |
| :--------- | :------------------- | :------------------------------ |
| `CHAR`     | `0-255`              | 定长字符串                      |
| `VARCHAR`  | `0-65535`            | 变长字符串                      |
| `TINYBLOB` | `0-255 `             | 不超过255byte大小的二进制字符串 |
| `TINYTEXT` | `0-255`              | 短文本字符串                    |
| `BLOB`     | `0-65535` 64k        | 二进制形式的长文本数据          |
| `TEXT`     | `0-65535`            | 长文本数据                      |
| MEDIUMBLOB | 0-16 777 215 `16M`   | 二进制形式的中等长度文本数据    |
| MEDIUMTEXT | 0-16 777 215         | 中等长度文本数据                |
| LONGBLOB   | 0-4 294 967 295 `4G` | 二进制形式的极大文本数据        |
| LONGTEXT   | 0-4 294 967 295      | 极大文本数据                    |

​    

## char和varchar的区别

`char`类型是固定长度的，最大长度为8字节，当存入的长度小于定义的长度则会以空格填充，查询时则会去掉空格再返回给客户端

所以`char`有下面几个特点:

- 当值开头结尾原本就包含空格的话则不适合用char存储
- 值是变长跨度很大的话则不适合用char，比较浪费空间
- char长度固定，所以不需要考虑边界问题，检索速度比vharchar快

`varchar`类型存储的是不固定长度的，最大长度为16字节，所以varchar需要在行记录里保存字段的实际长度

​    

## 参考

[MySQL 数据类型](https://www.runoob.com/mysql/mysql-data-types.html)