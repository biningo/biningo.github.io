---
title: Redis底层数据结构和时间空间复杂度
date: 2021-06-10
categories: [Redis]
tags: [Redis]
draft: true
---

## Redis数据类型和底层数据结构

| 数据类型 | 底层数据结构                        |
| -------- | ----------------------------------- |
| string   | RAW(object对象和SDS分配在一起)、SDS |
| list     | quicklist(链表节点是ziplist)        |
| set      | hashtable、intset(有序、存整数)     |
| sort set | skiplist、ziplist                   |
| hash     | hashtable、ziplist                  |

