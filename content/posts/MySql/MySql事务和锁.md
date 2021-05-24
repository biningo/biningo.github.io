---
title: MySql事务和锁
date: 2021-02-19
categories: [Mysql]
tags: [数据库,MySql]
---

## 为什么需要有事务

事务的出现就是为了 **保障数据一致性**，将多条逻辑相关的SQL语句当做一件事情来做，要么都成功要么都失败

最经典的场景就是转账业务: 当A给B转账时，B账户余额增加的同时必须保证A账户余额减少，也就是说必须保证A和B账户的总钱款大小前后一致，两个账户的钱总量不会多也不会少

事务的执行过程中最需要关注的就是 **事务并行和事务隔离** 问题，就是说多个事务必须是隔离的，事务A不能影响事务B的执行，这也是事务中最难处理和最难理解的一部分

​     

## 事务四大特性ACID

**原子性**  `Atomicity` 

事务是数据库的逻辑工作单位，不可分割，事务中包含的各操作要么都做，要么都不做

**一致性** `Consistency`

一致性是指事务将数据库从一种一致性状态变为下一种一致性状态，在事务开始之前和之后，数据库的完整性约束没有被破坏

比如转账的例子，两个账户的钱款总量在转账前后还是一致的，如果B余额增加了但是A余额没有扣除那么就会凭白无故多出一部分钱来，这就引发钱款不一致的状况了

再比如A的余额为0了，此时A还要给别人转账就不合适了，因为余额必须`>=0`。如果余额出现负数，这也导致了数据不一致状况

还有年龄不能为负数，红绿灯只有三种颜色，人民币最大面值为100等，这些约束都必须要符合真实世界的情况，都必须和真实世界保持一致性

**隔离性** `Isolation` 

每个事务都是隔离的，互相不影响，一共有`4`个隔离级别

**持久性** `Durability`

事务一旦提交，它对数据库中的数据的改变就应该是永久性的，必须持久化到磁盘

​     

## 四大隔离级别

### 1、读未提交

`Read Uncommitted`

一个事务还没提交时，它做的变更对他事务可见

- 脏读
- 不可重复读
- 幻读

### 2、读已提交

`Read Committed` **Oracle默认的隔离级别**

一个事务只有提交时它做的变化才对其他事务可见，该级别会造成 **在事务中两次读取数据不一致的情况，也就是不可重复读**

- 不可重复读
- 幻读

### 3、可重复读

`Repeatable Read` **MySql、Innodb默认的隔离级别**

一个事务开始之后，其所看见的数据就是事务开始时候的数据，相当于给数据在事务开始时拍一个快照，在事务执行过程中看见的都是这个快照，即使是其他事务做了变更提交了对此事务也不可见，**解决了不可重复读问题** 

但是还是存在 **幻读问题**

### 4、串行化

事务必须串行化执行，一个事务只有等另外一个事务结束之后才可以开始，效率最低不支持并发，但是解决了事务隔离性的所有问题

​    

## 事务并发读写问题

### 1、脏读

**事务读取到了其他事务还没提交的修改**

`A`事务执行过程中，`B`事务做出的变化还没提交修改就被A事务读取到了，但是`B`事务的修改发生了回滚，那么`A`事务就读取到了脏数据

| 时间线 | 存款事务                                                     | 取款事务                                                     |
| ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1      |                                                              | 开始事务                                                     |
| 2      | 开始事务                                                     |                                                              |
| 3      |                                                              | 查询余额500                                                  |
| 4      |                                                              | 取款400，余额更改为`500-400=100`                             |
| 5      | 查询余额100（脏读）                                          |                                                              |
| 6      |                                                              | 取款操作发生错误，事务失败，执行回滚操作 rollback （使用`commit`或者`rollback`后，事务就结束了） |
| 7      | 存入500，更改余额为`500+100=600`                             |                                                              |
| 8      | 提交事务                                                     |                                                              |
| 9      | 账户出错，按照正确的逻辑此时余额应该为 `500+500=1000`，而此时余额为`600` |                                                              |

### 2、不可重复读

**事务读取到了其他事务已经提交的修改，导致同一个事务两次查询结果不一样**

（一个已经提交了的事务修改了另外一个没有提交事务中读取过的数据）

`A`事务在执行过程中读到了`B`事务已经提交了的修改，造成`A`事务在执行过程中两次读取数据不一致

| 时间线 | 存款事务                                                     | 取款事务                 |
| ------ | ------------------------------------------------------------ | ------------------------ |
| 1      | 开始事务                                                     |                          |
| 2      | 查询余额`500`                                                |                          |
| 3      |                                                              | 开始事务                 |
| 4      |                                                              | 取款`500`，跟新余额为`0` |
| 5      |                                                              | 提交事务                 |
| 6      | 查询余额`0` （发送不可重复读，同一个事务中两次查询结果不一样） |                          |
| 8      | 提交事务                                                     |                          |

### 3、幻读

**幻读是指A事务在执行过程中查询一个范围的记录前后记录查询的结果不一样，会多出一行或则少掉一行，会出现幻行**

为什么会出现A事务前后查询结果不一样呢？因为A事务如果在事务执行过程中可以看见其他事务提交的变更，那么就会产生前后查询记录不一样，所以 **读已提交隔离级别会造成幻读问题**

| 时间线 | 事务A                                             | 事务B        |
| ------ | ------------------------------------------------- | ------------ |
| 1      | 开始事务                                          |              |
| 2      | 第一次查询数据一共100行                           |              |
| 3      |                                                   | 开始事务     |
| 4      |                                                   | 插入50行记录 |
| 5      |                                                   | 提交事务     |
| 6      | Update全表，影响数据150行（出现了幻行，产生幻读） |              |

其实幻读也是由不可重复读造成的，因为在同一个事务中读取的数据不一致

假设`a`表初始数据为0条

| 事务A                     | 事务B                                                        |
| ------------------------- | ------------------------------------------------------------ |
| `insert into a value(1);` | `select count(*) from a;` 结果是0条                          |
| `commit;`                 |                                                              |
|                           | `select count(*) from a;` 结果是0条                          |
|                           | `update a set id=99 where id=1` 结果提示影响一行数据，刚才查询还是没有数据的，但是现在更新却提示更新了一条数据，就像出现幻觉一样，这就是 **幻读** |
|                           | `select * from a` 这就会出现不可重复读现象读取到事务A已经提交了的数据了 |
|                           | `select count(*) from a;` 此时`count=1`                      |
|                           | `rollback` 此时执行回滚 `id=1`又回滚到了事务A提交时的状态    |

​    

## 什么时候代表事务结束

- `commit`提交事务的时候
- 再次执行`begin`开启事务时，如果上一个事务还没有提交则会进行自动提交

- `rollback`执行时则也代表此次事务结束了
- 出现`DDL`语句时也会提交事务 (`alter table`，)

​            

## 读写锁

### 读锁(S锁、共享锁)

对于未加修饰的`select`语句则不会加任何锁，我们也可以显示的在select后面加上`share mode`表示加上读锁

InnoDB是支持行锁的，下面展示只在行上面手动加S锁，事务提交之后自动释放

```sql
--此时其他事务不能对这行进行修改操作
select * from stu where id=1 lock in share mode
```

表锁

```sql
--session1
LOCK TABLE stu READ;
select * from stu; --允许读
update stu set name='A' where id=1; --报错 不允许写

--session2
select * from stu; --允许读
update stu set name='A' where id=1; --阻塞 直到读锁释放

--session1
unlock tables; --此时session2的写操作得以执行
```

### 写锁(X锁、排他锁)

对于`update、insert、delete`语句会默认会加上写锁，普通`select`也可以加上`for update`显示的加上X锁

```sql
--select语句加X锁 此时其它事务不能对这行进行修改操作，也不能对这行加上S锁，否则阻塞
select * from table where id=1 for update 
```

```sql
--此update会自动在这行加上X锁
update stu set name='A' where id=2;
```

表锁

```sql
--session1
LOCK TABLE stu WRITE，school WRITE; --同时给两张表加读锁

--session2
select * from stu; --读操作阻塞

--session1
unlock tables; --此时session2读操作得以执行
```

   

## 悲观锁和乐观锁

### 悲观锁

悲观锁会在每次执行之前先加锁，适合冲突大、并发量大的场景

```sql
--事务A
select * from stu for update;  --for udpate就表示加锁了

--事务B
select * from stu for update; --阻塞直到事务A提交 
```

### 乐观锁

在每次去拿数据的时候认为别人不会修改，不对数据上锁，但是在提交更新的时候会判断在此期间数据是否被更改，如果被更改则提交失败

乐观锁适合并发小，冲突小的场景，如果在冲突大的场景下每次执行完毕之后判断出已经被别人修改了则相当于白白执行了一次，会比较浪费资源

可以加一个`version`字段来实现乐观锁

```sql
--事务A
UPDATE stu SET age=age-10,version =version+1 WHERE version=0;
--事务B
UPDATE stu SET age=age-10,version=version+1 WHERE version=0;

--先执行的事务则成功执行，后执行的事务则条件判断失败
```

​      

## 事务隔离级别和锁

```bash
mysql> desc stu;                                                 
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int          | NO   | PRI | NULL    | auto_increment |
| name  | varchar(100) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
```

```sql
insert into stu(name) values('a'),('b'),('c'),('d'),('e'),('f'│
),('g');
```

```bash
mysql> select * from stu;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | b    |
|  3 | c    |
|  4 | d    |
|  5 | e    |
|  6 | f    |
|  7 | g    |
+----+------+
```

假设是在`RR`隔离级别

```sql
--事务A
begin;
update stu set name='A' where id=1; --此时事务A会将这行锁住

--事务B
begin;
select * from stu; --此时事务B无法看见A的变更
update stu set name='AA' where id=1; --此时事务A阻塞

--事务A
commit;  --此时事务A会释放行锁

--此时事务B才得以执行
--事务B
commit;

--此时 id=1的name=AA
```

```sql
--事务A
begin;
update stu set name='B' where name='b'; 

--事务B
begin;
update stu set name='BB' where name='b'; --此时事务A阻塞

--事务A
commit; --此时事务A会释放行锁

--此时事务B才得以执行
--事务B
commit;

--此时 name=BB
```

### 索引以及表锁行锁

当使用索引字段为`where`时只会锁行

```sql
--事务A
update stu set name='E' where id=5; --锁行

--事务B
update stu set name='F' where id=6; --不会阻塞 (如果也是修改id=5则会阻塞)
```

而使用非索引字段当`where`条件则会升级为表锁

```sql
--事务A
update stu set name='B' where name='BB';   --锁表

--事务B
update stu set name='C' where id=3; --阻塞
```

### 范围锁

查询指定一个范围时也会造成此范围内的记录锁定，不在此范围内则不会加锁

```sql
--事务A
update stu set name='A' where id<5;

--事务B
update stu set name='G' where id=7; --不会阻塞
update stu set name='a' where id=1; --阻塞 直到A提交
```

​    

## Mysql事务命令

```mysql
SHOW VARIABLES LIKE '%AUTOCOMMIT%';
SET AUTOCOMMIT = 0; #取消事务自动提交 on|off

SHOW VARIABLES LIKE 'transaction_isolation';  #查询当前的事务隔离级别
#修改系统的隔离级别  每次修改之后需要重新连接服务器才能生效
# read uncommitted 读未提交
# read committed 读已提交
# repeatable read 可重复读 (MySql默认的隔离级别)
# serializable 串行化
set global transaction isolation level repeatable read;

begin;
commit;
rollback;
```

​    

## 参考

[『MySQL』深入理解事务的来龙去脉](https://juejin.cn/post/6844903827611582471)

[MySQL数据库InnoDB引擎行级锁锁定范围详解](https://segmentfault.com/a/1190000013307132)

- https://juejin.cn/post/6844904115353436174#heading-4
- https://www.zhihu.com/question/47007926 【幻读】
- https://blog.csdn.net/mxw2552261/article/details/93263578 
- [周瑜Mysql事务](https://www.bilibili.com/video/BV1W64y1u761?p=9)