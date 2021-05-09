---
title: MySql执行计划
date: 2021-05-07
categories: [Mysql]
tags: [数据库,MySql] 
draft: true
---

## Explain

`Explain`是查看SQL语句执行的详细计划，比如多表连接的循序、索引使用的是哪一个等等

```bash
explain select * from test;
```

Explain会有下面几个列

| 列            | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | 每个select有一个id，如果是类似于`union`、子查询等这种查询则会显示多个select的查询计划，也就是多个id |
| select_type   | SELECT查询的类型                                             |
| table         | 查询的表                                                     |
| partitions    | 匹配的分区信息                                               |
| type          | 单表查询方法(const、ref、range....)                          |
| possible_keys | 可能用到的索引                                               |
| key           | 实际用到的索引                                               |
| key_len       | 实际使用的索引长度                                           |
| ref           | 当索引使用等值查询时，与索引列匹配的对象信息                 |
| rows          | 预估需要读取的记录数                                         |
| filtered      | 经过过滤之后剩余记录与预估记录数的百分比                     |
| extra         | 额外的信息                                                   |

​    