---
title: Go处理error
date: 2021-05-23
categories: [编程语言/Go]
tags: [Go]
---

## 大道至简的error

go的错误处理就只有一个`errors`包和一个`error`接口，这个接口只包哈一个`Error`方法，该方法返回一个string，这个包的代码很少，只有两个文件：

- `errors.go`
- `wrap.go`

go的error也就是通过创建一个错误提示的字符串的方式，然后通过返回值来返回这个错误，除非这个函数能保证一定能执行成功，否则每个函数都必须返回一个`error`

```go
func f() error{
	return errors.New("error") //返回error接口
}
```

下面来看下`errors/errors.go`的源码，不过10行左右

```go
func New(text string) error {
	return &errorString{text} //返回errorString指针
}

//实现了error接口
type errorString struct {
	s string
}

//获取错误字符串的方法
func (e *errorString) Error() string {
	return e.s
}
```

​     

## 自定义error

我们只需要实现`error`接口也就是重写`Error()`方法即可自定义错误

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

​    

## 优雅处理error

有时候，我们会写这样的一堆判断`error`的屎山代码：

```go
if err := a(); err != nil {
    return err
}
if err := b(); err != nil {
    return err
}
if err := c(); err != nil {
    return err
}
if err := d(); err != nil {
    return err
}
if err := e(); err != nil {
    return err
}
```

我们可以使用如下方式优雅处理`error`

```go
func main() {
	if err := task(); err != nil {
		log.Fatal(err)
	}
}

//复杂检查是否有error 如果有则引发panic
func check(err error) {
	if err != nil {
		panic(err)
	}
}

//panic() + recover()
func task() (err error) {

	defer func() {
		if r := recover(); r != nil {
			return
		}
	}()

	_, err = a("one")
	check(err)

	_, err = b(1, 2)
	check(err)

	err = c()
	check(err)

	err = d()
	check(err)
	return nil
}

func a(msg string) (string, error) {
	return msg, errors.New("a error")
}

func b(x, y int) (int, error) {
	if y == 0 {
		return 0, errors.New("b error")
	}
	return x / y, nil
}

func c() error {
	return errors.New("c error")
}

func d() error {
	return errors.New("d error")
}
```

​    

## Go1.13之后的error

Go1.13之后，允许我们嵌套的将多个`error`合并为一个，将最终的`error`返回给最上层，其源代码在`errors/wrap.go`中，一共有三个方法: 

- `Is`：判断一个error对象是否是另外一个error对象的封装，大的是小的，小的不是大的
- `Unwrap`：去除一层`error`封装

```go
func main() {
	e1 := errors.New("a") 		  // a
	// %w 是wrap的意思
	e2 := fmt.Errorf("%w->b", e1) // b->a
	e3 := fmt.Errorf("%w->c", e2) // c->b->a
	e2 = errors.Unwrap(e3)        // b->a
	e1 = errors.Unwrap(e2)        // a
    //true
	fmt.Println(errors.Is(e2, e1), errors.Is(e3, e2), errors.Is(e3, e1))
}
```

​      

## 参考

- https://coolshell.cn/articles/21140.html
- https://www.flysnow.org/2019/09/06/go1.13-error-wrapping.html
- https://draveness.me/golang/docs/part2-foundation/ch05-keyword/golang-panic-recover