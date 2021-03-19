---
title: Spring总结
date: 2021-03-19
categories: [Spring]
tags: [Spring] 
---

## Spring控制反转IOC和依赖注入DI

### 1、XML配置文件DI注入

- **Set注入**

Set注入是调用对应的Set方法来实现的，方法名需要遵循 `setName` 形式，必须采用小驼峰法，否则无法注入，**set注入首先会调用无参构造器创建类**

```xml
<bean id="hello" class="di.model.User">
    <property name="username" value="lyer"/>
    <property name="age" value="18"/>
</bean>
```

set注入可以注入多个数据类型的属性，常见的有如下几个类型

```bash
map array list 基本类型 set  bean(对象)
```

```xml
@Data
public class Student {
    private Integer id;
    private String name;
    private List<String> hobby;
    private Map<String,Integer> scores;
    private Set<String> friends;
    private Address address;
}
```

```xml
  <bean id="stu" class="di.model.Student">
        <property name="id" value="1"/>
        <property name="name">
            <null/> #显示指定为null 默认不设置值也为null
        </property>
        <property name="scores">
            <map>
                <entry key="english" value="100"/>
                <entry key="chinese" value="99"/>
            </map>
        </property>

        <property name="friends">
            <set>
                <value>xioamin</value>
                <value>hong</value>
            </set>
        </property>


        <property name="hobby">
            <list>
                <value>running</value>
                <value>basketball</value>
            </list>
        </property>

		#内联bean 也可以ref引用外部bean <ref bean="address" />
        <property name="address">
            <bean class="di.model.Address">
                <property name="postcode" value="1111"/>
                <property name="province" value="zj"/>
            </bean>
        </property>
    </bean>
```

- **构造器注入**

对象需要对应的构造方法，默认的`set`注入是调用无参构造器创建类的，但是构造器注入就需要特定的构造器方法支持

```xml
<bean id="user" class="di.model.User">
        <constructor-arg name="username" value="lyer"/>
        <constructor-arg name="age" value="18"/>
</bean>
```

- **bean作用域**

`singleton` 默认作用于 **单例**

```xml
<bean id="userInfo" class="cn.lovepi.UserInfo" scope="singleton"></bean>
```

`prototype` 每次对该Bean请求的时候，Spring IoC都会创建一个新的作用域

```xml
<bean id="userInfo" class="cn.lovepi.UserInfo" scope=" prototype "></bean>
```

其它的作用域用的不多，不举例了

​    

### 2、xml引入合并

Spring可以讲所有的xml配置文件合并为一个配置，只需要`import`即可

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <import resource="beans.xml"/>
</beans>
```

​    

### 3、bean的自动装配

如果一个对象里面引用另外的对象，那么我们需要在xml配置文件中一个个指定这些`bean`和对应`property`的`ref`值，那么很多代码都是重复性的操作，比如下面

```xml
<bean id="door" class="di.model.house.Door" p:name="door"/>
<bean id="window" class="di.model.house.Window" p:name="window"/>
<bean id="house" class="di.model.house.House" p:name="house">
        <property name="door" ref="door"/>
        <property name="window" ref="window"/>
</bean>
```

Spring有一个自动装配的特性，可以根据IOC容器中的对象自动赋值给对象的属性，这样就可以省略property的书写了

```xml
<bean id="door" class="di.model.house.Door" p:name="door" />
<bean id="window" class="di.model.house.Window" p:name="window" />
<bean id="house" class="di.model.house.House" p:name="house" autowire="byName" />
```

- `byName`  

    自动装配会去查找类的`set`方法，然后按照`bean`的命名约定`setXXX` 最后根据后半部分的名字去容器里面查找对应的bean去装配上去

- `byType` 

    类型的bean必须只有一个

也可以启动注解方式进行自动装配，注解开启之后就不需要写`set`方法即可完成注入，开启注解方式自动装配有如下几个步骤:

- 引入xml的`context`的命名空间
- xml中添加对注解的支持`<context:annotation-config />`
- `@Autowire` `@Resource`

> **@Autowire和@Resource的区别:**
>
> - `@Autowire`通过byType来查找bean，并且必须要存在，可以配合`@Qualifier("xxx")`来查找特定id的bean
> - `@Resource` 默认根据byName进行查找，找不到再根据byType    

​    

### 4、Spring注解注入

注解开发需要添加注解的支持，并且要添加扫描包的配置

```xml
<context:annotation-config />
<context:component-scan base-package="di" />
```

将对象加入IOC容器的主要有如下几个 注解，下面的注解功能都是一样，名字不一样是为了MVC分层

```bash
@Component
@Service
@Repository
@Controller
```

```java
@Component
public class Dog {
    @Value("haba") //相当于xml<bean>中的value 注意 这里也不需要set方法
    private String name;
    @Value("19")
    private Integer age;
}
```

```java
public static void main(String[] args) {
    ApplicationContext context = new ClassPathXmlApplicationContext("all.xml");
    Dog dog = context.getBean("dog", Dog.class);
    System.out.println(dog);
}
```

​    

### 5、Java配置类注入

用Java的配置类来**替换xml的注解**，需要使用不同的`Context`对象来获取对应的bean，**注意bean的名字是方法名** 使用配置类+注解就可以完全抛弃xml配置文件了

```java
@Data
@AllArgsConstructor
public class Cat {
  private String name;
}
```

```java
@Configuration
public class AppConfig {
  @Bean //这样就不需要在Cat上加@Component了
  public Cat generateCat() {
    return new Cat("cat");
  }
}
```

```java
public static void main(){
    ApplicationContext aContext = new AnnotationConfigApplicationContext(AppConfig.class);
    //注意bean的名字是方法名
	Cat cat = aContext.getBean("generateCat",Cat.class);
	System.out.println(cat);    
}
```

也可以在配置类上加上`@ComponentScan`来添加扫描包，这样就完全不需要在xml里配置扫描了

```java
@Configuration
@ComponentScan("com.cds.adsa")
public class AppConfig {
  @Bean //这样就不需要在Cat上加@Component了
  public Cat generateCat() {
    return new Cat("cat");
  }
}
```

案例请看 [https://github.com/biningo/spring-play/tree/main/spring-annotation-play](https://github.com/biningo/spring-play/tree/main/spring-annotation-play)

​    

## 控制反转IOC和依赖注入DI

**IOC** `Inversion of Control` 是一种思想理念，而 **DI** `Dependency Injection`是实现IOC的一种方式

先说一说什么是控制反转，简单来说就是将控制权反转过来，比如计算机可以连接很多外设比如硬盘、U盘、屏幕、打印机等，操作系统需要为每一个接口都写一套程序以此来操作这些设备，这样每增加一个设备操作系统就需要写一套程序，这样未免也太麻烦了，所以操作系统就开发了一套接口，设置了一些读取数据的规则，现在外部设备想要被操作系统操作只需要实现操作系统的接口，自己写一套驱动程序，然后将驱动程序安装到操作系统中即可操作设备，这样就反转了控制权，依赖也倒置了

- 原本控制权都在操作系统中，操作系统为每个设备写了驱动程序，现在控制权都到了各个外设中，外设自己写驱动程序

- 原来设备需要依赖操作系统为自己写驱动程序，现在操作系统需要依赖设备提供相关的驱动

在Spring中也是一样的，原来创建对象都需要程序员自己进行创建，创建对象的代码写死在程序中，一旦用户的需求变了，那么就需要程序员手动修改代码

Spring采用了依赖注入的方式解决了上面的问题，程序员需要写一个接口和一些实现类，或则实现类由用户自己写，那么用户就可以自行选择实现类进行创建然后注入到系统中即可，比如用`Set`方法进行注入对象

Spring还使用IOC容器来管理和创建所有对象，IOC再进行注入对象

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/ioc.jpg)

​    



## 静态代理

## 动态代理

## Spring的AOP使用

AOP有三种方式

- 通过实现接口，XML配置文件方式
- 切面类，配置XML文件
- 纯注解方式

具体案例请看: [https://github.com/biningo/spring-play/tree/main](https://github.com/biningo/spring-play/tree/main)

​    

## Spring总结

Spring核心主要有如下几点:

- **IOC控制反转和DI依赖注入(Spring容器)**
- **动态代理和AOP切面**
- 事务的支持

​    

## 参考

https://zhuanlan.zhihu.com/p/97886967

https://coolshell.cn/articles/9949.html