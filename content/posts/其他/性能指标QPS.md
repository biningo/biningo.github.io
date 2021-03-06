---
title: 性能指标QPS
date: 2021-06-03
categories: [其它]
tags: [时间]
---

## TPS每秒事务数

`Transactions Per Second` 每秒处理的事务数目，一个事务是指一个客户机向服务器发送请求然后服务器做出反应的过程

比如我请求访问twitter页面，则twitter服务器会返回一个twitter完整的页面给我，这个页面可能还包括一些css、js、各种数据的请求，这一整个页面的访问和全部数据的响应就叫TPS

`TPS`过程包括: 客户端请求服务端+服务端内部处理+服务端返回客户端

```bash
tps=处理事务数/秒
```

​    

## QPS每秒查询率

`Queries Per Second` 每秒查询率，表示一台服务器每秒能够响应的查询次数，代表的是服务器的机器的性能最大吞吐能力

`qps`基本类似于 `tps`，但是不同的是对于一个页面的一次访问，形成一个 TPS，但一次页面请求，可能产生多次对服务器的请求，服务器对这些请求就可计入QPS之中

简单来说QPS就是对每个后端请求的度量，比如一个页面要请求多个接口，那么对每个接口的请求可以归结为QPS，当用户请求页面到页面完整的展示给用户这一个过程可以归结为TPS

也就是说如果一个页面只会访问一个接口一个请求，则`TPS=QPS`

```bash
qps=能处理的请求数/秒
```

​    

## PV页面浏览量

`page view `页面浏览量，用户每次对页面的访问都计入`PV`，比如我对一个页面刷新了5次那么PV就加5

​    

## UV独立访客数

`Unique Visitor` 独立访客访问数，可以理解为IP访问次数，比如我对一个页面刷新了100次，但是UV还是只加1，因为IP还是我