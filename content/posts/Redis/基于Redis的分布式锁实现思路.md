---
title: 基于Redis分布式锁实现思路
date: 2021-05-05
categories: [Redis]
tags: [数据库,Redis,分布式] 
---

## 为什么需要分布式锁

单机应用中(也就是一台机器中)的锁用于控制当前程序多个线程并发而引发的资源争夺问题，但是当应用场景扩展到了分布式环境就不一样了，这样的锁就没用了。因为每台机器都是独立的，如果还是之前的锁的话只能保证本机器不会引发资源竞争问题，分布式环境下相当于每台机器都有一个自己独立的锁，所以无法避免资源竞争问题

这个时候就需要将这把锁保存到第三方中 （比如`Redis`），多台机器同时到Redis中取抢锁，这就可以保证分布式环境下争抢的是同一把锁

​    

## Redis分布式锁实现思路

首先，Redis里面并没有锁的概念。所谓的锁其实就是Redis里的一个`key`，加锁就是设置这个`key`，释放锁就是删除这个`key`

一个进程如果在请求加锁的过程中发现这个key已经存在了则表示加锁失败，这个锁已经被别人持有了。如果发现这个key不存在则表示这个锁没有被人持有

Redis中可以借助如下命令来实现判断如果没有锁再加锁操作:

```bash
#此命令表示如果key不存才会设置key并且返回1 如果是普通的set则会覆盖
setnx lock 1
```

单纯的这样实现分布式锁貌似太简单了，并且有个问题: **死锁**

也就是说如果一台机器设置了锁，在执行过程中宕机了或出错了，那么这把锁将永远得不到释放，其他机器进程就永远无法获取到锁，引发死锁问题

于是我们可以借助Redis的key过期功能来给锁设置一个过期时间，这样就不用怕锁永远得不到释放了。同时需要注意设置key以及对应的过期时间这一系列动作应该是原子的，否则在设置key时还没来得及设置过期时间这台机器又宕机了还是会引发死锁问题。

综上，我们使用Redis提供的`set`命令以及参数来实现

```bash
#如果key不存在则设置lock并且过期时间为10s (nx表示不存在才会设置 存在则失败返回0)
set lock 1 ex 10 nx 
```

但是这样还会引发一个锁被错误释放问题

想象一下下面的场景:

- A加锁执行，但是在莫个操作上面阻塞很久，此时锁过期了被自动释放
- B获得锁继续执行
- 在B执行过程中A从阻塞中恢复了，并且A执行完毕了
- 于是A再次释放了锁（注意此时A释放的是B设置的锁）但是此时B还在执行中
- 此时C过来了，于是C就抢到锁了

这样就引发了一个错误: **在B加锁执行过程中，B的锁被错误的释放了**

为了解决锁被错误释放的问题，我们需要给锁设置一个唯一标识，这个锁标识了是哪个进程加的锁，并且只有加锁的进程本身才能释放这把锁

```bash
set lock <uuid> ex 10 nx
```

实现思路就是线程在释放锁的时候获取一下key对应的value也就是锁的标识，判断一下是否是自己的那把锁。

如果不是自己的说明自己的锁已经超时被自动释放了则不会再次释放别人的锁，如果是自己的则进行释放

完整代码:

```go
//redis分布式锁的实现
type Lock struct {
	RedisClient *redis.Client
	Key         string
	UUID        string
	Expire      time.Duration
}

func NewLock(key string, expire time.Duration) *Lock {
	uuid, err := exec.Command("uuidgen").Output()
	if err != nil {
		log.Fatal(err)
	}
	return &Lock{
		RedisClient: global.RedisCli,
		Key:         key,
		UUID:        string(uuid),
		Expire:      expire,
	}
}

func (lock *Lock) Acquire() bool {
	v, err := lock.RedisClient.SetNX(context.Background(), lock.Key, lock.UUID, lock.Expire).Result()
	if err != nil {
		panic(err)
	}
	return v
}

func (lock *Lock) Release() bool {
	if v, _ := lock.RedisClient.Get(context.Background(), lock.Key).Result(); v == lock.UUID {
		_, err := lock.RedisClient.Del(context.Background(), lock.Key).Result()
		if err != nil {
			panic(err)
		}
		return true
	}
	return false
}
```

​    

## 参考

[阿里架构师1小时带你搞懂分布式锁](https://www.bilibili.com/video/BV1va4y1j7Qe?p=1)

《Redis使用手册》

