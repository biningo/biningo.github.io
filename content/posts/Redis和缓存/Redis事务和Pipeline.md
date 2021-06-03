---
title: Redis事务和Pipeline
date: 2021-05-26
categories: [Redis]
tags: [事务,Redis]
draft: true
---

## Pipeline管道

Redis的`pipeline`是属于客户端范畴，当客户端调用`Exec()`时才会将之前管道队列里的请求一起打包发送给服务器，服务器收到打包过来的命令之后还是和普通指令一样将指令放入缓存队列然后一条条执行，区别在于客户端会等待管道命令都得到回复之后一起返回给用户应用

**综上，pipeline的出现是为了节约网络带宽和提高传输效率，将多条命令打包传输到服务器中执行，并且客户端在等待**

管道命令需要注意下面几个问题:

- **管道不是原子和隔离的**

    在执行管道命令的过程中是可以被其他命令打断的，也就是说管道执行不是原子的

- **管道不是一致的**

    如果管道中其中一条命令执行失败了不会影响其他命令的执行，其他命令依然可以执行成功

下面看一段go代码演示`pipeline`

```go
func main() {
	p := global.RedisCli.Pipeline()
	p.Get(context.Background(), "email")
	p.Set(context.Background(), "name", "lyer", 0)
	p.Set(context.Background(), "age", "18", 0)
	results, _ := p.Exec(context.Background())
    
	email := results[0].(*redis.StringCmd).Val()
	s1 := results[1].(*redis.StatusCmd).Val()
	s2 := results[2].(*redis.StatusCmd).Val()
	fmt.Println(email, s1, s2)
}
```

​    



