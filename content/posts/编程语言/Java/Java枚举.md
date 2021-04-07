---
title: Java枚举
date: 2021-04-04
categories: [编程语言/Java]
tags: [Java]
---

## Java枚举的简单使用

```java
public enum HttpStatus {
  OK, NoContent,
  NotFound,
  InternalServerError, BadGateway
}
```

可以使用如下几个常见的枚举方法

```java
public static void main(String[] args) {
    HttpStatus ok = HttpStatus.OK;
    String okStr = ok.name(); //OK
    Integer okIndex = ok.ordinal(); //0
    HttpStatus notFound = HttpStatus.valueOf("NotFound"); //str->enum
    HttpStatus[] values = HttpStatus.values(); //string values
}
```

​     

## 枚举实现原理

枚举是Java的一种语法糖，内部通过继承 `Enum`抽象类来实现，下面我写了一个类来说明枚举类实现原理，就是通过 `static`**变量**+`static`**初始代码块** 来实现的，这就是为什么枚举非常适合作单例的原因

```java
public final class WeekDay {
  private final String name;
  private final Integer ordinal;

  private WeekDay(String name, Integer ordinal) {
    this.name = name;
    this.ordinal = ordinal;
  }

  public String getName() {
    return this.name;
  }

  public Integer getOrdinal() {
    return this.ordinal;
  }

  public static final WeekDay Mon;
  public static final WeekDay Tue;
  public static final WeekDay Wed;
  public static final WeekDay Thu;
  public static final WeekDay Fri;
  public static final WeekDay Sat;
  public static final WeekDay Sun;
  private static final WeekDay[] $Values;

  static {
    Mon = new WeekDay("Mon", 0);
    Tue = new WeekDay("Tue", 1);
    Wed = new WeekDay("Wed", 2);
    Thu = new WeekDay("Thu", 3);
    Fri = new WeekDay("Fri", 4);
    Sat = new WeekDay("Sat", 5);
    Sun = new WeekDay("Sun", 6);
    $Values = new WeekDay[]{
            Mon, Tue, Wed, Thu, Fri, Sat, Sun
    };
  }

  public String toString() {
    return this.name;
  }

  public static WeekDay[] values() {
    return $Values.clone();
  }

}
```

每个枚举对象都有一个`name`和`ordinal` 分别为枚举属性名对应的名字以及他们的序号(0,1,2,3.....)，枚举对象都继承于`Enum`抽象对象，该对象有构造方法并且有`name`和`ordinal`属性，创建的时候会根据属性名字来创建

```java
 //构造器
private Week(String name, int ordinal){
    super(name, ordinal);
}
#下面请看关键代码
static{
	OK=new HttpStatus("OK",0);
    NotFound=new HttpStatus("NotFound",1);
    //...
    $VALUES = (new HttpStatus[] {
            OK,NotFound...
        });
}
//生成静态方法values，克隆一份数组，也就是调用这个方法之后就会返回一份包括枚举类中所有实例的数组
public static HttpStatus[] values(){
    return (HttpStatus[])$VALUES.clone();
}
//还有上面的name ordinal方法都有，直接返回name和ordinal
```

​    

## 丰富枚举类功能

上面写的`HttpStatus`类功能太单一，根据上面的实现原理，我们可以自定义

```java
public enum HttpStatus {
  OK("successful!", 200), NoContent("没有内容!", 204),
  NotFound("抱歉页面找不到", 404),
  InternalServerError("服务器内部错误", 500), BadGateway("网关错误", 502);

  private final String msg;
  private final Integer code;

  HttpStatus(String msg, Integer code) {
    this.msg = msg;
    this.code = code;
  }


  public String getMsg() {
    return this.msg;
  }

  public Integer getCode() {
    return this.code;
  }

  public String toString() {
    return this.msg;
  }
}    
```

```java
public static void main(String[] args) {
    HttpStatus ok = HttpStatus.OK;
    String msg = ok.getMsg();
    Integer code = ok.getCode();
}
```

​    

## 参考

[说说JAVA枚举这玩意吧](https://juejin.cn/post/6844903959073832967)

[编译与反编译，让字节码说人话](https://juejin.cn/post/6844903993815072775)

