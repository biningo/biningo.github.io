---
title: MySql字符集
date: 2021-02-16
categories: [Mysql]
tags: [数据库,MySql] 
---

## MySql字符集

MySql支持很多字符集，可以使用如下命令查看MySql支持的字符集:

```mysql
SHOW CHARACTER SET
SHOW CHARSET
SHOW CHARACTER SET LIKE  'utf8%'
```

其中`Default collation` 表示默认的 **比较规则** `MAXlen`表示最大长度（单位byte）

下面是几个重要的字符集的最大长度:

| 字符集名称                                     | Maxlen |
| ---------------------------------------------- | ------ |
| ascii                                          | 1      |
| gbk                                            | 2      |
| utf8（阉割版UTF8，不包含特殊字符如表情字符等） | 3      |
| utf8mb4                                        | 4      |

​    

## utf8和utf8mb4

`utf8`是`utf8mb3`的别名，只支持最大长度是`3byte`，属于 **阉割版** 的UTF8字符集

`utf8mb4` 是完整的UTF8字符集，最大支持`4byte`，包含所有UTF8字符集

**所以如果文本里面有特殊符号或则表情符号，比如存博客等就需要修改表的字符集为utf8mb4**

​    

## 字符集比较规则

字符集比较规则用于字符集比较，比如`a、A`两个比较，是按照二进制大小来比较还是忽略大小写进行比较

可以使用如下命令显示支持的比较规则

```mysql
SHOW COLLATION #显示所有比较规则
SHOW COLLATION LIKE '%utf8%'
```

`utf8`默认的比较规则就是`utf8_general_ci` 

`utf8mb4`默认是`utf8mb4_0900_ai_ci`

> 以ci结尾的比较规则都是忽略大小写的

​    

## 字符集和比较规则应用范围

分别有3个应用范围:

- 服务器级别
- 数据库级别
- 表级别
- 列级别

### 1、服务器级别

| 系统变量             | 描述                 |
| -------------------- | -------------------- |
| character_set_server | 服务器级别的字符集   |
| collation_server     | 服务器级别的比较规则 |

可以在配置文件里配置服务器的字符集和比较规则

```toml
[server]
character_set_server=gbk
collation_server=gbk_chinese_ci
```

### 2、数据库、表、字段级别

数据库级别的字符集和比较规则可以在建库的时候指定，**如果没有指定则默认和服务器级别的字符集一样**

| 系统变量               | 描述                 |
| ---------------------- | -------------------- |
| character_set_database | 数据库级别的字符集   |
| collation_database     | 数据库级别的比较规则 |

```mysql
CREATE DATABASE mydb
CHARACTER SET utf8
COLLATE utf8_general_ci
```

库的字符集变量是只读的，只能在创建库的时候指定或则使用`ALTER`指令修改

```mysql
ALTER DATABASES mydb CHARACTER SET gbk;
```

表级别也一样可以在建表的时候指定 **如果没有指定则和库的字符集一样**

```mysql
CREATE TABLE mytb(....)
ENGINE=InnoDB #指定引擎
CHARACTER SET=utf8
COLLATE=utf8_general_ci
```

```mysql
#要查看表的字符集和其他信息
SHOW TABLE STATUS from lyer like 'article' #lyer库   article表
#修改表的字符集
ALTER TABLE article CHARACTER SET gbk
```

列级别则是直接在字段里面指定，不指定默认和表级别一样

```mysql
CREATE TABLE tb(
	username varchar(100) CHARACTER SET utf8 COLLATE utf8_general_ci
)
```

```mysql
#要查看表所有的列信息
SHOW FULL COLUMNS FROM article
#修改列字符集
ALTER TABLE tb MODIFY username VARCHAR(10) CHARACTER SET gbk COLLATE gbk_chinese_ci
```

修改字符集或则比较规则需要注意:

- **只修改字符集,则比较规则将变为修改后的字符集默认的比较规则**
- **只修改比较规则,则字符集将变为修改后的比较规则对应的字符集**

​    

## 客户端和服务器通信字符集

几个系统变量:

| 系统变量                 | 解释                                                         |
| ------------------------ | ------------------------------------------------------------ |
| character_set_client     | 服务器解码请求时使用的字符集                                 |
| character_set_connection | 服务器处理请求时会把请求字符串从 character_set_client 转为 character_set_connection |
| character_set_results    | 服务器向客户端返回数据时使用的字符集                         |

客户端和服务器通信过程如下:

- 客户端使用系统的字符集进行编码转化为二进制发送到服务器
- 接受到二进制会认为客户端使用的是`character_set_client`解码方式，于是就使用`character_set_results`进行解码转化为字符串
- 然后用`character_set_connection`进行编码，转化为此编码的字符串，然后进行SQL语句解析和数据查询
- 查询出来的数据再用`character_set_results`进行编码为二进制然后发送给客户端
- 客户端再根据自己的系统的字符集进行解码为字符串

上述编解码过程可能会出现如下几个问题:

- 客户端编码和`character_set_client`不一致，服务器无法读懂
- `character_set_results`和客户端编码不一致，客户端无法无法读懂

**所以综上，将上述三个系统变量都设置为一致，并且和客户端字符集保持一致**

可以使用如下命令:

```mysql
SET NAMES utf8
#上面命令相当于
SET character_set_client = utf8
SET character_set_connection = utf8
SET character_set_results = utf8
```

​    

## 参考

《MySql是怎样运行的》