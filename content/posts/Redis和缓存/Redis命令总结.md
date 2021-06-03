---
title: Redis命令总结
date: 2021-03-16
categories: [Redis]
tags: [数据库,中间件] 
---

## Keys

`COPY` 复制key

```bash
COPY name username #name->username username必须不存在
COPY name username DB 1 #name->1:username
COPY name username REPLACE #存在则更新
```

`DEL` 删除key 返回值是删除的个数

`UNLINK`非阻塞删除，重新开辟一个线程去回收内存，立即返回

```bash
DEL key1 key2
```

`EXISTS` 查看可以是否存在 返回值是存在键的数量

```bash
EXISTS age username
```

`EXPIRE` `EXPIREAT` 设置key的过期时间，如果重新设置的key的值，前者是设置n秒后过期，后者设置一个Unix时间戳，表示在指定时间戳后过期

`TTL` 查看key剩余的时间·`秒`，返回`-1`则表示永久 `-2`则表示key不存在，`PTTL`则是毫秒

`PERSIST` 解除`timeout`时间

```bash
EXPIRE name 10
EXPIREAT name 8233132131
TTL name 
PERSIST name
```

`KEYS` 模式匹配展示出keys

```bash
KEYS name* # * 匹配所有
KEYS name? # ? 匹配一个
```

`MOVE` 移动key到指定的db

```bash
MOVE age 2
```

`OBJECT` 展示redis每个key的对象相关的信息，比如对象底层的数据结构是什么

redis有五大常见对象:

- String
- Hash
- List
- Set
- Zset

要判断key属于上面上面哪个类型可以使用`TYPE`命令来查看

```bash
TYPE name
```

每种对象的底层编码方式都是不一样的

```bash
KEYS name* #* 表示匹配所有
KEYS name? #? 表示匹配一个字母
```

`MOV`

```bash
OBJECT ENCODING name #查看底层数据结构
OBJECT REFCOUNT name #查看该对象的被引用次数
OBJECT IDLETIME name #查看没有被访问的空闲时间(秒)
```

`RANDOMKEY` 随机返回一个key

```bash
RANDOMKEY
```

`REMAME` 重命名key，如果新的key存在则会覆盖，新的key的值就会丢失，所以可以使用`RENAMENX`来修改，如果不存在则才会rename，存在则返回`0`

```bash
RENAME name newName
RENAMENX name newName
```

`TOUCH`更新key的访问时间

```bash
TOUCH name
```

【?????】 `SCAN` 游标遍历key，普通的key匹配无法限制输出的个数，scan可以  

```bash
SCAN 0
SCAN 0 MATCH ? COUNT 2 #匹配一个字母的key  并且限制2个数量
```

`SORT` 排序list、set等

```bash
SORT arr
SORT arr DESC
```

​    

## String

`APPEND` 追加，如果key不存在则新建一个，返回值是追加的长度

```bash
APPEND name lyer
```

`INCR/DECR` 对整数进行加1减1 不存在会创建，0为初始值

`INCRBY/DECRBY` 加减一定的数

```bash
INCR a
DECR b
INCRBY a 10
```

`GETDEL` 获取之后就删除

```bash
GETDEL name #之后再获取就没了
```

`GETRANGE` 获取string指定范围的字符串

```bash
GETRANGE name 0 3 #[0,3] [0,-1]全部
```

`GETSET` 获取老值同时设置新值

```bash
GETSET name abc
```

`GETEX` 获取值，同时设置过期时间

`SETEX` 设置值并设置过期时间

```bash
GETEX k EX 10
SETEX k 10 v
```

`MGET` 获取多个key，`get`只能获取一个key

`MSET` 设置多个key，`set`只能设置一个key

```bash
MGET k1 k2 k3
MSET k1 v1 k2 v2
```

`SETNX/MSETNX` 只有不存在才设置值，存在则不设置 msetnx其中有一个设置不成功则全部不成功

```bash
SETNX k v
```

`SETRANGE` 从指定位置开始设置值

```bash
SETRANGE k 10 aaaaa #10位置开始aaaaa 如果原来长度小于10则默认设置0
```

`STRLEN` 计算key的长度

```bash
STRLEN a
```

​    

## BitMap

`BITCOUNT` 计算val的`1`的个数，val对应的都是字符串形式保存，会转化为`ascii码`然后转化为二进制，包括数字也是会转化为ascii码

`GITBIT/SETBIT`  获取/设置指定位置的bit位,setbit如果不存在key则会创建，并且以8位为单位进行适当的扩容

```bash
a-z：97-122
A-Z：65-90
0-9：48-57
```

```bash
SET name a #01100001
BITCOUNT name #3
BITCOUNT name 0 3 #[0,3] 2
GETBIT name 0 #0:0 1:1 7:1
SETBIT name 0 1 #11100001
```

`BITOP` 用于位运算相关操作，dest不存在则会创建，存在则覆盖

```bash
BITOP <opt> <dest> <key1> <key2> ...
```

有如下几个操作符

```bash
AND、OR、XOR、NOT
```

```bash
BITOP AND a b c
```

​    

## HyperLoglog计数器

`PFADD` 将值添加到BloomFilter中

```bash
PFADD names lyer
```

`PFCOUNT` 计算有多少个元素

```bash
PFCOUNT names
```

`PFMERGE` 合并两个布隆过滤器

```bash
PFMERGE dest src #将src合并到dest中 src不变 dest是合并之后的
```

​    

## redis运维

### CONFIG

此命令用于redis相关配置的设置和读取，具体内容参考官方文档

```bash
CONFIG GET databases #数据库数量 databases 16 
```

### INFO

以一种易于理解和阅读的格式，返回关于 Redis 服务器的各种信息和统计数值，不同于`CONFIG`命令，此命令还会展示redis服务器运行时的动态数据

```bash
INFO #展示所有的信息
INFO server #展示特定的信息
INFO keyspace #展示每个数据库的键的数量等相关统计信息
```

其他的参考官方文档 https://redis.io/commands/info

