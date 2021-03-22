---
title: HashMap源码分析
date: 2021-03-12
categories: [编程语言/Java]
tags: [HashMap,源码分析]
---

## 为什么对象需要同时重写equals和hashcode

Object对象有两个重要的方法: `equals` `hashcode`

```java
public boolean equals(Object obj) {
        return this == obj; //默认直接比较是否是同一个引用
}
@HotSpotIntrinsicCandidate
public native int hashCode();
```

`equals` 默认是直接比较是否是同一个引用

`hashCode` 则不同机器实现不一样，一般是用变量的内存地址来进行计算hash值的

如果一个对象要放入 **HashSet** 或则 **HashMap** 的话，需要遵循下列原则，否则就会出现不可预期的错误(HashSet是用HashMap来实现的，所以只需要讨论HashMap即可):

> **如果一个类重写了equals()方法，则必须重写hashCode()方法。2个对象的equals()方法返回true的话，其hashCode()必须返回相同的值**

**为什么需要重写 hashCode() 方法呢?**

这个很好理解，因为HashMap查找和放入一个 **kv** 必须计算出key的hash值，然后选择合适的位置放入value，其实就是直接调用key的`hashCode()`方法计算hash值，下次查找就是直接根据hash值来直接索引到value(这里还需要考虑Hash碰撞问题)，这样就实现了`O(1)`速度的查找，HashMap是典型的空间换时间的例子

如果我们不重写`hashCode()`方法，则会调用父类Object的hashCode方法，该方法是根据值得内存地址计算得出的，也就是说我们不重写的话，那么两个我们认为值相同的key对象计算出的hash值就会不一样，这样就会导致错误，比如HashSet无法进行去重等，下面的Student没有重写hashCode方法

```java
public static void main(String[] args) {
        HashSet<Student> set = new HashSet<>();
        Student s1 = new Student("one");
        Student s2 = new Student("one");
        set.add(s1);
        set.add(s2);
        System.out.println(set.size()); //2 【错误】这里应该为1
}
```

**已经重写了hashCode()，为什么还需要重写equals()方法？**

虽然我们重写了`hashCode()` 方法，但是还是无法得出正确的结果，因为hash值可能会发生 **hash碰撞** ，也就是两个不同值得对象计算出的hash值一样，这个时候就会用 **拉链法** 等算法来解决碰撞，所以如果只比较`hashCode`就认为两个对象是相等的这显然是不合理的，所以 **HashMap** 在添加和取对象的时候不仅仅会比较`hashCode`，还会比较`equals`方法，如果两个都相等才判断为同一个对象

上面的案例如果只重写了`hashCode`，HashMap进而会比较他们的equals()，但是上面没有重写equals()，Object对象默认equals行为就是`==`，所以equals比较不等，HashMap则认为这是发生了Hash碰撞，所以还是会将相同的key加入进来

所以我们必须重写 `equals`和`hashCode`方法这样才能达到知自己预期的效果

​    

## HashMap源码分析

HashMap刚开始是一个`Node`节点的数组，叫做 **HashTable** ，初始化为`16`大小 如果节点元素个数达到一定的值(这个值略小于 **HashTable** 的大小)，则会进行扩容

然后逐渐放入元素，刚开始如果发生碰撞就采用 **拉链法** 进行解决碰撞 ，如果链表的节点个数`>=8`同时 **HashTable** 大小大于`64`  则会进行树化，转化为 **红黑树** 来加快查找(红黑树是一颗高度平衡的二叉查找树)  如果链表节点个数大于`8`但是HashTable小于`64`则会进行扩容HashTable



