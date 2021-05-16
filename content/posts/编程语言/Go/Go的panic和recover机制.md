---
title: Go的panic和recover机制
date: 2021-05-10
categories: [编程语言/Go]
tags: [Go]
draft: true
---

`panic`和`recover`机制

```go
func main(){
    defer func() {
		err := recover() //此处接受到的就是panic的入参：error类型
		log.Println("main:", err) 
	}()
	
    //模拟任务执行
	time.Sleep(1)
    //引发恐慌 结束程序之前会执行defer
	panic(errors.New("hello,error"))
	//下面得不到执行了
    //....
}
```

在并发的情况下，如果一个`goroutine`引发`panic`则父亲的`recover`是收不到的，也就是说`recover`只能恢复自己当前的`gotoutine`的一个panic

```go
func main() {
	defer func() {
		err := recover()
		log.Println("main:", err)
	}()

	wg := sync.WaitGroup{}
	wg.Add(1)
	go func() {
		time.Sleep(1)
        //此处引发的panic无法被上面的recover收到 所以程序崩溃了
		panic(errors.New("error!")) 
	}()
	wg.Wait()
}
```

上面的程序稍加修改，边可以收到了

```go
func main() {
	wg := sync.WaitGroup{}
	wg.Add(1)
	go func() {
		defer wg.Done()
		defer func() {
			err := recover()
			log.Println("goroutine:", err)
		}()
		time.Sleep(1)
		panic(errors.New("error!"))
	}()
	wg.Wait()
}
```

