---
title: Go优雅的处理error
date: 2021-02-15
categories: [编程语言/Go]
tags: [Go]
---

## 大道至简的error

go的错误处理就只有一个`errors`包和一个`error`接口，这个接口只包哈一个`Error`方法，该方法返回一个string，这个包的代码很少，只有两个文件：

- `errors.go`
- `wrap.go`

go的error也就是通过创建一个 **错误提示的字符串** 的方式，然后通过返回值来返回这个错误，基本每个函数的返回值都标配一个error对象以及函数本身的返回值：

- 函数本身返回值
- `error`对象

最简单的创建错误的方式就是通过`errors.New`来创建：

```go
func f() error{
	return errors.New("error")
}
```

下面来看下`errors.go`的源码，不过10行左右

```go
func New(text string) error {
	return &errorString{text}
}

// errorString is a trivial implementation of error.
//实现了error接口
type errorString struct {
	s string
}

//获取错误字符串的方法
func (e *errorString) Error() string {
	return e.s
}
```

综上来看，我们只需要实现`error`接口即可自定义错误（很多包都有自定义错误，可以参考他们的实现）：

```go
type ZeroDivisionError struct {
	msg  string
	code int
}
func (e ZeroDivisionError) Error() string {
	return fmt.Sprintf("[%d]:%s", e.code, e.msg)
}
type NullPointerError struct {
	msg string
}
func (e NullPointerError) Error() string {
	return fmt.Sprintf("NullPointerError:%s", e.msg)
}

//----------------------------------
func main(){
    e := IntNull()
	switch e.(type) {
	case NullPointerError:
		log.Println("Null:", e)
	case ZeroDivisionError:
		log.Println("Zero Division:", e)
	default:
		log.Println("undefined")

	}
}
```

​    

## error地狱

有时候，我们会写这样的一堆判断`error`的屎山代码：

```go
if err := f(); err != nil {
    return err
}
if err := f(); err != nil {
    return err
}
if err := f(); err != nil {
    return err
}
if err := f(); err != nil {
    return err
}
if err := f(); err != nil {
    return err
}
```

我们可以简化成一个`error` 下面用一个临时函数来处理`error` 并且设置了一个全局变量`err`   这样`err`就是调用链最后的`error`信息

```go
var err error
f2 := func() {
    err = f()
    if err != nil {
        return
    }
    //.....
}
f2()
f2()
f2()
if err != nil {
    return err
}
```

我们也可以将`error`集成到`struct`中，在结构体中设置一个`error`对象，如果有错误直接检查这个属性即可

在处理`error`的时候还要避免重复处理，也就是说要将`error`返回给调用链的最上层，在中间不进行处理，为了打印错误调用链，也就是为了弄清楚是哪里出错了，我们可以讲多个`error`合并为一个返回给最上层，这就是`Go1.13`提供的`Wrap`

​    

## Go1.13之后的error

Go1.13之后，允许我们嵌套的将多个`error`合并为一个，将最终的`error`返回给最上层，其源代码在`errors/wrap.go`中，一共有三个方法：

- `As` ：将一个error直接赋值于另外一个error对象
- `Is`：判断一个`error`对象是否是另外一个error对象的封装，大的是小的，小的不是大的
- `Unwrap`：去除一层`error`封装

```go
e1 := errors.New("a")
e2 := fmt.Errorf("b->%w", e1) //%w 是wrap的意思 该方法返回一个error b->a
e3 := fmt.Errorf("c->%w", e2) // c->b->a
e22 := errors.Unwrap(e3) // b->a Unwrap掉了一层
//e22 is e2,e1    e22 is not e3
errors.Is(e22, e2),errors.Is(e22, e1), errors.Is(e22, e3))

e11 := errors.Unwrap(e22) //a
log.Println(errors.Is(e11, e1), errors.Is(e11, e2), errors.Is(e11, e3))

//As将后面的直接copy前面的
e4 := errors.New("e4") 
errors.As(e4, &e3) // e3="e4"
errors.Is(e3, e4), e3 == e4) // true true
```

​    

## panic和recover

关于`panic`和`recover`，我们必须注意

- `panic` 只会触发当前 Goroutine 的 `defer`；
- `recover` 只有在 `defer` 中调用才会生效；
- `panic` 允许在 `defer` 中嵌套多次调用

```go
defer func() {
    e := recover() //panic传入什么 这里就接受什么
    err := e.(error) //都是interface类型
    log.Println(err)
}()
//....
panic(errors.New("panic error"))
//....
```

`panic`只会在当前`goroutinue`中生效，下面只会打印`"goroutine"`就引发`panic`结束进程了，并不会执行`"main"`：

```go
func main() {
    defer func(){
        log.Println("main")
        recover()
    }
	go func() {
		defer log.Println("goroutine")
		panic("panic")
	}()
	time.Sleep(time.Second)
}
```





## 参考

- https://coolshell.cn/articles/21140.html
- https://www.flysnow.org/2019/09/06/go1.13-error-wrapping.html
- https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-panic-recover