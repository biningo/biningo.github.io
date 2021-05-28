---
title: Java集合总结和源码浅析
date: 2021-03-21
categories: [编程语言/Java]
tags: [Java集合,源码分析]
---

## OverView

![](https://raw.githubusercontent.com/biningo/cdn/master/img1/java-co.jpeg)

​        

## HashMap和HashTable

HashMap刚开始是一个`Node`节点的数组 ，初始化为`16`大小 如果节点元素个数达到一定的值(这个值略小于 **HashTable** 的大小)，则会进行扩容

然后逐渐放入元素，刚开始如果发生碰撞就采用 **拉链法** 进行解决碰撞 ，如果链表的节点个数`>=8`同时 数组 大小大于`64`  则会进行树化，转化为 **红黑树** 来加快查找(红黑树是一颗高度平衡的二叉查找树)  如果链表节点个数大于`8`但是数组小于`64`则会进行扩容数组

`HashTable`和`HashMap`的主要区别如下:

- **HashTable是并发安全的，HashMap不安全**
- **HashTable的value不允许null，HashMap允许**
- HashTable属于老的集合容器，HashMap属于新一代集合容器

​        

## ConcurrentHashMap

和`HashMap`差不多，但是这个是并发安全的

在 JDK1.8 中，ConcurrentHashMap 选择了与 HashMap 相同的**数组+链表+红黑树**结构，在锁的实现上，**采用 CAS 操作和 synchronized锁** 实现更加低粒度的锁，将锁的级别控制在了更细粒度的 table 元素级别，也就是说**只需要锁住这个链表的首节点，并不会影响其他的 table 元素的读写，大大提高了并发度**

​    

## LinkedHashMap

LinkedHashMap能保持插入的顺序，**HashMap+双链表** 实现

在插入的时候同时给每个节点都按照插入的顺序串成一个双链表(**LinkedHashMap给之前HashMap中的链表节点多加了两个前后节点实现**)，这样遍历的时候就可以保持插入的顺序了，同时用`key`取值得时候效率还和 **HashMap** 一样

下面两个

```java
  public static void main(String[] args) {
    LinkedHashMap<String, String> m1 = new LinkedHashMap<>();
    m1.put("name", "lyer");
    m1.put("age", "18");
    m1.put("score", "100");
    m1.forEach((k, v) -> System.out.println(k + "-" + v));

    System.out.println("--------------------------");

    HashMap<String, String> m2 = new HashMap<>();
    m2.put("name", "lyer");
    m2.put("age", "18");
    m2.put("score", "100");
    m2.forEach((k, v) -> System.out.println(k + "-" + v));
  }
```

​    

## TreeMap

TreeMap使用 **红黑树** (一种高度平衡的二叉搜索树) 来实现 **K-V** 存储，根据搜索树的特性可以保持`key`有序，这样查找一个`key`就可以利用二分来快速查找

​    

## ArrayList、Vector、LinkedList

**ArrayList**

- ArrayList底层是一个`Object[]`数组
- 在添加元素的时候如果发现容量不足则以初始容量的 `1.5`倍速度扩容
- 初始容量默认值是`10`
- ArrayList不是并发安全的

**Vector**

- Vector底层也是`Object[]`数组
- 在添加元素的时候如果发现容量不足则以初始容量的 `2`倍速度扩容
- 初始容量默认值也是`10`
- Vector是并发安全的，都在方法级别加上了`synchronized`

**LinkedList**

- 底层是一个 **双链表**  

- 不是并发安全的

综上： `LinkedList`适合用于大量插入、删除操作的场景  `ArrayList`适合用于频繁查找的场景(数组和链表的优缺点)

​    

## Set

各种 **Set** 都是使用Map作为底层实现的，Value都是Object对象，所以这里不再单独讲解

​    

## 参考

https://blog.csdn.net/ThinkWon/article/details/104588551

https://zhuanlan.zhihu.com/p/40760616

