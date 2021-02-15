---
title: Go随机数用法
date: 2021-02-15
categories: [编程语言/Go]
tags: [Go]
---

## 基本用法

go随机数在`math/rand`包下，go的随机数需要先给他一个`Seed`，`Seed`如果一样的话或则不设置的话每次生成的都是 **伪随机数** ，多次执行生成的都是一样的随机数序列，所以必须设定`Seed`而且还是以 **时间戳** 的方式来设置，如下生成 `[0,10)`之间的随机整数：

```go
rand.Seed(time.Now().UnixNano())
r:=rand.Intn(10) //[0,10) 返回int类型
r=rand.Int63n(10) //返回int64
....
```

如果要生成指定范围的随机整数，如下生成`[min,max)`之间的随机整数：

```go
rand.Seed(time.Now().UnixNano())
max:=10;min:=-10
rand.Intn(max-min)+min) //[-10,10)
```

​    

## 随机负载均衡实现

我们实战一下，实现一个 **随机数负载均衡**

```go
type RandomBalance struct {
	curIndex   int
	hosts []string
}

func (r *RandomBalance) Add(host string) {
	r.hosts = append(r.hosts, host)
}

func (r *RandomBalance) Next() (string, error) {
	if len(r.hosts) == 0 {
		return "", errors.New("no host")
	}
	rand.Seed(time.Now().UnixNano())
	r.curIndex = rand.Intn(len(r.hosts))
	return r.hosts[r.curIndex], nil
}
```

```go
func main() {
	rb := RandomBalance{}
	for i := 1; i < 10; i++ {
		rb.Add("h" + strconv.Itoa(i))
	}
	for i:=0;i<10;i++{
		log.Println(rb.Next())
	}
}
```

