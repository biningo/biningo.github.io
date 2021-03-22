---
title: Java注解和反射
date: 2021-03-20
categories: [编程语言/Java]
tags: [Java]
---

## 为什么需要注解

注解也是`Java 1.5`开始引入的，注解产生是为了替代早期的`XML`配置文件的，早期使用配置文件XML

其中注解与代码强耦合，一旦改变就需要重新编译，而XML配置文件则和代码分开，修改XML配置文件不需要重新编译因为XML原本就是不编译进字节码中的，Java代码通过文件读取的方式获取XML配置文件的信息

但是注解的优点就是方便简洁好维护，而XML则不方便不容易维护而且配置复杂

​    

## 元注解

**元注解** 是一种注解在注解上的一种注解，由Java标准库提供，我们自定义注解必须要标注上元注解用于说明这个自定义注解的范围等

元注解有如下4个:

- `@Target` 表示注解可以注解在方法上还是类上
- `@Retention` 表示注解保留在什么范围 ，一般注解和反射配合使用，所以一般这个值为`runtime` 注解可以保留到程序运行的时候，它会被加载进入到 JVM 中，所以在程序运行时可以获取到它们
- `@Documented ` 将注解中的元素包含到 Javadoc 中
- `@Inherited` 子类可以继承父类的注解

​    

## 自定义注解

如果注解只有一个值，则需要设置为`value()`这样注解上就不需要key指定了

```java
@Target(ElementType.METHOD)
public @interface My {
  String value() default ""; //可以赋值默认值
}
```

直接这样使用即可，如果有多个则必须要指定key，没有指定默认值得必须写出值，否则可以不写使用默认值

```java
@My("A")
```

注解也可以定义数组，数组元素只有一个时不需要花括号包裹

```java
@Target(ElementType.METHOD)
public @interface My {
  String value() default "";
  String[] names();
}
```

```java
@My(value="A",names={"lyer","b"})
```

​    

## 为什么需要反射

首先来看反射能做什么我们才可以知道为什么需要反射机制了:

- 有了反射我们可以在程序运行时动态的加载类，只需要给定类的全路径名即可，而不需要硬编码代码中
- 反射相当于给了程序员一个**后门** 可以破坏封装，直接获取一个类的所有属性或则方法，让我们了解一个陌生类的所有属性和方法，知道这个类里面都有什么东西，并且可以创建这个类执行里面的私有方法等

知道反射能干啥之后我们就可以得出为什么需要反射机制了:

- **方便测试**

- **运行时动态加载类，不需要编译时确定写死** (并且有些类无法在编译之前就知道需不需要加载，只有在运行时才知道是否需要加载，这时候就需要反射了，比如数据库驱动，用户可能选择不同的数据库，所以程序需要更具用户的XML配置等来决定加载哪个数据库的驱动) 

    ```java
    if (condition_a) {
        Class cls = Class.forName("A");
    else if (condition_b) {
        Class cls = Class.forName("B");
    } else {
        Class cls = Class.forName("Other");
    }
    ```

- **了解一个陌生类的内部结构(包括私有方法和属性)**

- 一个类没有公共的构造方法，只能通过反射来创建，或则一个网络传递过来的`.class`字节码的类而无法new对象以及不知道类的内部结构时，只能通过反射了解对象和创建对象了

- **和注解配合**

> 有了反射之后，创建一个对象就有两种方法了: **new、反射**

​        

## Java中反射对象

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/java-reflection.jpg)

要反射一个类首先第一步就是需要获取一个类的 `Class`对象，每个类都有一个`Class`对象来记录这个类的方法、属性、构造方法等类相关的东西，这个类是JVM为每个对象创建的，所以首先我们需要获取到这个类的Class对象

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/java-class.png)

获取`Class`对象有三个方法，三个方法获取的都是同一个`Class`对象，因为一个类只有一个`Class`对象:

```java
//stuClass1==stuClass2==stuClass3 其中第一种最常用
Class<Student> stuClass1 = (Class<Student>) Class.forName("tmp.Student");
Class<Student> stuClass2 = Student.class;
Class<Student> stuClass3 = (Class<Student>) new Student().getClass();
```

然后我们就可以通过`Class`类来获取到:

- `Field` 代表类中的属性的对象，可以通过这个获取属性的名字、权限范围等
- `Constructor` 类的构造方法对象，可以获取构造方法对应的参数列表
- `Method` 类中方法对象，可以获取方法的名字等，并且可以通过Mehtod中的`invoke`方法执行类的方法

> 带有`Declared`的方法就是表示获取所有，而不带的则表示获取public的

​      

## 通过反射创建对象实例

反射创建对象可以通过`Constructor`对象创建，这属于反射对象的一种，代表类的构造方法对象

```java
Constructor<Student> constructor = stuClass1.getConstructor(String.class, Integer.class);

Student lyer = constructor.newInstance("lyer", 18);

System.out.println(lyer.getName()+"-"+lyer.getAge());
```

这种反射创建对象的方式有时候会破坏一些模式，比如 **单例模式** 单例模式的构造方法都是私有的，为的就是保持单例而不然用户随意创建，而反射却能随意的调用私有的构造方法创建对象

​    

## 反射对运行速度的影响[不是很懂]

反射除了破坏了面相对象的封装性，还会带来运行速度上的影响，为什么反射这么慢主要有如下几个原因:

- Method#invoke 方法会对参数做封装和解封操作
- 需要检查方法可见性
- 需要校验参数
- 反射方法难以内联
- JIT 无法优化
- 调用一个方法必须根据给出的方法名遍历方法集合然后调用(存在数组遍历的损耗)，调用属性也是一样的

不是很懂，反正就是慢就对了

​        

## 反射获取注解

注意，只有`@Retention` 为`runtime`才能被获取

```java
//获取方法上的注解
My my = stuClass1.getMethod("getName").getAnnotation(My.class));
my.age()// 直接获取各个值
```

​    

## 参考

https://juejin.cn/post/6844904144222814221

https://zhuanlan.zhihu.com/p/86293659

https://juejin.cn/post/6844904018326585352

https://www.codenong.com/cs109745018/ 【反射和new的区别】

https://juejin.cn/post/6844903965725818887【反射效率低的原因】

