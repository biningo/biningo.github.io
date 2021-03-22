---
title: Java泛型总结
date: 2021-03-20
categories: [编程语言/Java]
tags: [Java]
---

## 为什么需要泛型

Java的泛型机制在`Java 5.0`中加入，为什么需要泛型呢?

在以前没有泛型的情况下，一个 `List` 类型要能存放所有类型那么底层只能是Object数组来接受所有类型，这样的话我们可以将所有类型都放入这个Lits中，从Lits中获取元素的时候再强转，这样很容易会引发 **ClassCastException异常**

再来想想，如果不使用Object会怎么样？

这样的话虽然可以避免 **ClassCastException异常** 但是就要为每种类型都写一个List类，增加了代码量

如果加入泛型之后就不会在运行时引发错误，因为错误都会在编译的时候就发现，泛型的引入能让编译器更好的优化和检查程序的前期错误而不是等到运行的时候再出现错误

并且也不需要为每种类型都写一个List了，直接在new的时候指定类型即可，相当于造了一个代码模具(因为这个代码对于每种类型的操作都是一样的)，使用的使用填充类型即可

总结一下为什么需要泛型:

- **强类型，编译器通过泛型实现类型检测，避免运行时类型转换错误，增强代码可读性**
- **对象重用，减少代码量**

​    

## 泛型在Java中的使用

泛型类和泛型方法的案例

```java
public class Pair<T, U> {
  private T t;
  private U u;

  public Pair(T t, U u) {
    this.t = t;
    this.u = u;
  }

  public T getT() {
    return this.t;
  }
  
  //泛型可以单独应用在方法上 调用: p.<String>("one")
  public <E> void echo(E e) {
    System.out.println(e);
  }
  
  public U getU() {
    return this.u;
  }
```

泛型还可以应用在 **接口** 上

```java
public interface Person<T> {
    public void show(T t);
}
//实现
public class PersonImpl implements Person<String> {
    @Override
    public void show(String s) {
        System.out.println(s);
    }
}
//泛型类实现泛型接口
public class PersonImpl<U> implements Person<U> {
    @Override
    public void show(U s) {
        System.out.println(s);
    }
}
```

泛型通配符`?` 为了能传入所有类型则可以直接设置类型为`?` 反射中相关的类大量用到了这个通配符，因为任何类都可以别反射，反射操作对于任何类型都是一样的

```java
List<?> arr = new ArrayList<>();
public interface Person<?> { 
}
```

也可以给通配符`?`泛型设定 **下限和上限**

```java
#只能传入Integer的父亲的类型
List<? super Integer> arr = new ArrayList<>();
#只能传入继承于Integer的类型
List<? extends Integer> arr = new ArrayList<>();
```

泛型限定，只能继承与一些类

```java
public interface Person<T extends String> { 
}
//&表示同时继承多个类
<T extends Serializable & Cloneable> 
//,表示继承其中一个
<T extends Serializable,Cloneable>     
```

​    

## JVM类型擦除

泛型只在编译阶段起作用，编译为字节码后，在JVM中都是同一个类型，这就是 **类型擦除** 但是为什么在写代码的时候如果放入不同类型数据报错呢? 因为泛型的类型擦除是先编译后擦除的，也就是说会先检查是否有编译错误，如果没有错误就进行类型擦除

这样的话我们的代码就不会出现 `CalssCastException`错误了，因为在编译的时候就已经保证了所有的类型都是正确的

```java
List<String> l1 = new ArrayList<String>();
List<Integer> l2 = new ArrayList<Integer>();
System.out.println(l1.getClass() == l2.getClass()); //true
```

上面的`l1` `l2` 到了JVM中都是同一个对象，都是 `List.class`   ，底层的类型都会被转化为最除的`Object`类型，如果泛型都上限则底层类型是他的上限类型

```java
public class Val<T> {
  T t;
  public Val(T t){
    this.t = t;
  } 
}
```

```java
Val<String> val = new Val<>();
```

类型擦除之在JVM字节码中就变成了

```java
public class Val {
  Object t;
  public Val(Object t){
    this.t = t;
  } 
}
```

可以使用下面的例子来验证

```java
public static void main(){
    Val<String> val = new Val<>("a");
	Class<?> classVal = val.getClass();	
	System.out.println(classVal.getName()); //Val
	System.out.println(classVal.getDeclaredFields()[0].getType());//Object
}
```

看有下限的例子

```java
public class Val<T extends String> {
  T t;
  public Val(T t){
    this.t = t;
  } 
}
```

类型擦除之后就变成了如下

```java
public class Val {
  String t;
  public Val(String t){
    this.t = t;
  } 
}
```

在泛型类被类型擦除的时候，之前泛型类中的类型参数部分如果没有指定上限，如 `<T>`则会被转译成普通的 `Object` 类型，如果指定了上限如 `<T extends String>`则类型参数就被替换成类型上限

​    

## 泛型总结

泛型主要需要注意如下几个点

- **类的泛型、接口泛型、方法泛型**
- **泛型通配符** `?`
- **泛型的上限extends和下限super**
- **JVM中的泛型类型擦除**

​    

## 参考

- https://juejin.cn/post/6844904196655824904
- https://www.zhihu.com/question/315232030
- https://blog.csdn.net/briblue/article/details/76736356