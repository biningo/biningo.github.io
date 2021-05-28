---
title: MySql时间处理
date: 2021-05-25
categories: [Mysql]
tags: [MySql,时间]
draft: true
---

## 时间类型

| 类型      | 大小 ( bytes) | 范围                                                         | 格式                                                         |
| :-------- | :------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| DATE      | 3             | `1000-01-01~9999-12-31`                                      | `YYYY-MM-DD`                                                 |
| TIME      | 3             | 表示一天内任意的时刻                                         | `HH:MM:SS`                                                   |
| YEAR      | 1             | `1901~2155`                                                  | `YYYY`                                                       |
| DATETIME  | 8             | `1000-01-01 00:00:00/9999-12-31 23:59:59`                    | `YYYY-MM-DD HH:MM:SS`                                        |
| TIMESTAMP | 4             | 格林尼治时间<br>开始时间`1970-01-01 00:00:00`<br>结束时间 `2038-01-19 03:14:07`<br>也就是第 **2147483647** 秒 | 底层使用4byte的int类型来存储秒数，客户端读取时会自动调用`from_unixtime()`并且转化为`YYYY-MM-DD HH:MM:SS`格式字符串 |

​    

## 获取当前时间

```sql
select
current_date(), --curdate()同理
current_time(), --获取当前datetime
current_timestamp(), --会自动调用from_unixtimestamp()转化为datetime
unix_timestamp(), --int时间戳
now() --current_time()同理
from dual;
```

​     

## 时间转化

> 在Mysql中`YYYY-mm-dd HH:MM:SS`格式的字符串会自动转化为时间类型，并且`timestamp`在存入或则读取的时候都必须是上面类型的，mysql会自动调用函数转化为int类型的秒数时间戳，如果传入时间戳反而会报错，如果你真的需要获取时间戳可以调用`unix_timestamp()`函数进行再次转化，时间戳也可以使用int类型保存

时间转化为时间戳

```sql
 --将指定时间时间转化为时间戳，没有指定时刻则默认为0时刻,
select UNIX_TIMESTAMP('2020-05-21') from dual;
select UNIX_TIMESTAMP('2020-05-21 18:10:20') from dual;

--datetime转化为时间戳
select UNIX_TIMESTAMP(NOW()) from dual;

--date转化为时间戳 time默认为0
select UNIX_TIMESTAMP(CURDATE()) from dual;

--time转化为时间戳 date默认是当前时间
select UNIX_TIMESTAMP(CURRENT_TIME()) from dual;
```

转化为时间

```sql
--时间戳转化为datatime
select FROM_UNIXTIME(UNIX_TIMESTAMP()) from dual; 
```

```sql
--转化为date类型
select DATE(CURRENT_TIME()) from dual;
select DATE('2020-05-25 10:10:20') from dual;

--时间戳不能直接转化为date，必须先转化为datetime才可以转化为date
select DATE(FROM_UNIXTIME(UNIX_TIMESTAMP())) from dual;
```

```sql
--datetime转化为time，其他类型不能直接转化为time，这里时间戳会自动调用from_unixtimestamp()转化为datetime
select TIME(CURRENT_TIMESTAMP()) from dual;
select TIME(NOW()) from dual; --效果一样，只不过这里直接获取了datetime
```

​    

## 时间运算

时间加减

```sql
select DATE_ADD(NOW(),interval 2 year)
select DATE_ADD(NOW(),interval 2 month) from dual;
select DATE_ADD(NOW(),interval 2 day) from dual;
select DATE_ADD(NOW(),interval 2 hour) from dual;
select DATE_ADD(NOW(),interval 2 minute) from dual;
select DATE_ADD(NOW(),interval 2 second ) from dual;

--加上负数就是减法
select DATE_ADD(NOW(),interval -2 day) from dual;
--可以传入date/datetime/time等类型
--如果是传入整数类型的时间戳则必须FROM_UNIXTIME()将时间戳转化为时间
--下面传入的字符串会自动转化为时间类型
select DATE_ADD('2021-01-01',interval 2 day) from dual;
select DATE_ADD(CURRENT_TIMESTAMP(),interval 2 day) from dual;
select DATE_ADD(FROM_UNIXTIME(UNIX_TIMESTAMP()),interval 2 day) from dual;
```

减法类似

```sql
select DATE_SUB(NOW(),interval 1 day) from dual;
```

