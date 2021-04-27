---
title: Go数组与slice
date: 2021-02-14
categories: [编程语言/Go]
tags: [Go]
---

## 数组

- **值拷贝**

```go
	a:=[...]int{1,2,3}
	a2:=a
	log.Println(a,a[0])
	log.Println(a2,a2[0]) //也可以通过下标访问
	a2[0] = 10
	log.Println(a) //a[0]=1
```

- **指针传递**

```go
//通过指针访问数组
	a:=[...]int{1,2,3}
	a2:=&a
	log.Println(a,a[0])
	log.Println(a2,a2[0])
	a2[0] = 10
	log.Println(a) //a[0]=10
```

​    

## 切片与数组

- **数组**

```go
array := [3]int{1,2,3}
array := [...]int{1,2,3}
```

- **切片**

```go
array := []int{1,2,3} //len=3 cap=3
array := make([]int,2) //len=2 cap=2

arr := [5]int{1,2,3,4,5}
//底层数组可见 会修改原数组 相当于原数组1-3的子数组指针
array :=arr[1:3] //cap=5-1=4  len=3-1=2
array[0]=99 //arr[1]=99 被修改了
log.Println(arr) //[1 99 3 4 5]
```

​    

## len和cap的区别

**len**:代表底层数组**可访问的范围** 用索引访问不可越过这个界限

**cap**:代表底层数组的**实际空间长度**，如果**append**元素时没有超过这个cap，则不再创建底层数组，否则开辟新的空间，同时增大cap（这里有一个增大规则），所以如果要频繁的扩容，适当设置大一些的**cap**还是能减少开销的（设置cap是为了防止多次申请内存，因为内存申请需要转化为内核态会造成一定消耗）

```go
array := make([]int,2,5) //len=2 cap=5
array := make([]int,2) //make([]int,2,2) 如果省略默认len=cap
```

```go
//1
arr:=make([]int,2,10)
arr[0]=1
arr[1]=2
//arr[2]=3 //报错
arr = arr[:6] //扩容 不会再申请空间 因为cap=10
arr[2]=3
log.Println(arr) // 1 2 3 0 0 0
//arr=arr[:11] //报错 超过cap的大小，此时必须用过append进行扩容

//2
arr := [10]int{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}
s := arr[:3:10] //s=[1,2,3]
s1 := s[:5] //s1=[1,2,3,4,5]
s2 := s[:7] //s2=[1,2,3,4,5,6,7]

//3、下面的切片引用切片  引用的相当于都是同一个 cap右界限都是和父亲一样的
a1 :=[]int{1, 2, 3, 4, 5, 6, 7, 8}
a2 :=a1[:3] //[1,2,3] 
a3 :=a2[:4] //[1,2,3,4]
a4 :=a3[:5] //[1,2,3,4,5]
```

​    

## 切片传参

```go
func Args(arr ...int){
    //arr其实就是一个切片类型
} 
Args(1,2,3,4,5)
```

​    

## 切片的底层结构

**runtime/slice.go**

```go
type slice struct {
	array unsafe.Pointer //指向底层数组的首地址
	len   int
	cap   int
}
```

​    

## 切片为什么是引用类型？

切片传入函数，同样也是值传递，会copy一份切片的值传入函数内，哪为什么又说是引用类型呢？为什么函数内部改变会影响原切片呢？

**根据上面的切片底层结构我们知道，切片有一个指向底层数组的指针，虽然切片是传值复制了一份，同时指向底层数组的指针也复制了一份，但指针始终是指向同一个地址的，那么我们改变切片的值其实就是改变底层数组的值。因为他们还是共享底层地址的。**

​    

## 空切片的判断

空切片可以判断它的长度是否为0，**但是判断为nil来判断这个切片是否为空是不准确的**

比如下面两种情况就要用len是否为0来判断：

```go
empty1:=make([]int,0)
log.Println(empty1==nil) //false
empty1=[]int{}
log.Println(empty1==nil)//false

b:=make([]int,10)
bb:=b[:0]
log.Println(bb)
log.Println(bb==nil) //false
```

下面的就是为nil  因为这只是声明，而没有开辟地址

```go
var empty2 []int
log.Println(empty2==nil)  //true
```

​    

## 切片扩容机制

切片在`cap<=1024`的时候扩容是2倍扩容，当`cap>1024`则扩容就是按照 `1.25~2`倍来扩容，这和go的内存分配机制有关，如果预估的容量的大小匹配到了一个指定的内存块，则新容量大小会以这个内存块大小为主

扩容的时候会将原来的地址数组的元素重新copy到扩容后新的底层数组空间上，也就是如果发生扩容那么底层数组就会改变

```go
a1 := make([]int, 1, 4)
a1[0] = 1              //[1]
a2 := append(a1, 2, 3) //[1,2] 此时没有引发扩容 所以底层数组引用没有改变
log.Println(a1, a2) //[1] [1,2,3]
a2[0]=888
log.Println(a1, a2) //[888] [888,2,3]
```

```go
a1 := []int{1, 2}   //[1,2] cap=2
a2 := append(a1, 3) //[1,2,3] cap=4 此时底层数组已经改变
a2[0] = 999 //底层数组改变 不会影响a1
log.Println(a1, a2) //[1,2] [999,2,3]
```

​    

## 切片的克隆

下面在 `i` 的位置插入一个元素，其他元素向后移动（前提是len够,否则后面的元素会溢出）

```go
a1:=[]int{1,2,3,4,5,6}
i:=1
a1=append(a1,0) //扩充一个空间
copy(a1[i+1:],a1[i:]) //将第i个元素向右边移动
log.Println(a1)//[1,2,2,3,4,5,6]
a1[i] = 88
log.Println(a1) //[1,88,2,3,4,5,6]
```

下面是可读性最高的一个 **切片copy** 代码

```go
 a := []int{1, 2, 3}
 b := make([]int, len(a)) //copy不会开辟空间 需要预先创建好空间
 copy(b, a)
```

用下面的方法也可以copy

```go
a := []int{1, 2, 3}
a2:=append(a[:0:0],a...) //a[:0] =[]
//下面两种情况的拷贝都只是a的引用 会相互影响
//a2:=append(a[:0],a...)
//a2:=a[:] 
log.Println(a2) //[1 2 3]
```

​    

## 切片的插入

插入的时候当cap不足时，会自动扩容，增加元素赋值的消耗，应该避免这样大容量的copy，并如果真的需要，则可以预先开辟大一点的`cap`，并且用大容量为 **左值**

```go
s:=[]int{1,2}
elements:=[]int{3,4,5,6}

// Push（插入到结尾）
//超过cap的值会自动扩容 此处最好用elements为左值
s = append(s, elements...)  //s = append(elements,s...)
// Unshift（插入到开头）
s = append(elements, s...) //在s的头部插入
//将切片elements的元素插入到s切片的i位置之后
s = append(s[:i], append(elements, s[i:]...)...)
```

​    

## 删除切片的元素

删除可以仅仅移动底层数组指针，**但是这种方式有一个缺陷，会造成内存泄漏，无法被GC回收不需要的内存**

```go
arr:=arr[1:]
```

下面这种方式先nil，然后再删除，不会内存泄漏

```go
arr[0]=nil
arr=arr[1:]
```

也可以将后面的元素全部向前移动，这种方式也不会造成内存泄漏

```go
a = [] int { 1 , 2 , 3 }
a = append (a[:0], a[1:]...) // 删除开头1个元素
a = append (a[:0], a[N:]...) // 删除开头N个元素
```

删除操作其实和插入的思想是一样的，删除一段切片，如下，删除了 **[1-3)** 的元素，删除后为[a,d,e,f] 注意是**前闭后开**的

```go
arrStrClone=[]string{"a","b","c","d","e","f"}
arrStrClone = append(arrStrClone[:1], arrStrClone[3:]...)
```

删除一个元素其实是删除一段元素的特例，这里就不举例了

​    

## 切片实现栈队列

出

```go
s, e = s[1:], s[0]  //shift操作 将开头元素弹出
s, e = s[:len(s)-1], s[len(s)-1] //pop 将尾元素弹出
```

入

```go
// Push（插入到结尾）
s = append(s, elements...)
// Unshift（插入到开头）
s = append(elements, s...)
```

​    

## for-range

range后面可以接受0个、1个、2个

1个值默认那个值是索引，2值后面那个是数组的值，前面是索引，遍历切片也是一样的

```go
var item [10]int

	for range item{
		log.Println("OK")
	}
	for i:=range  item{
		log.Println(i)
	}
	for i,v:=range item{
		log.Println(i,"-",v)
	}
```

​    

## 关于并发

**注意，go里面的特殊容器都是线程不安全的，多个并发读取可以，但是并发修改是不允许的，需要加以控制**

