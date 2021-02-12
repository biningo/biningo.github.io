---
title: Maven和Gradle帮我们干了什么
date: 2021-02-08
categories: [JVM和Java]
tags: [JVM,Java,Maven,Gradle]
draft: true
---

## 徒手编译java代码

以前我们都是用 **IDEA** 点击运行项目就跑起来了，甚至有些人完全不知道Java代码的编译过程，下面我们来徒手编译一波java代码，主要使用了`javac`编译工具

```java
cloud
│       └── lyer
│           ├── App.java
│           └── MyNumber.java
---------------------------------------------------------
    
package cloud.lyer;
public class MyNumber {
    public int Sum(int a, int b) {
        return a + b;
    }
}

package cloud.lyer;
public class App {
    public static void main(String[] args) {
        System.out.println("---------App------------");
        System.out.println(new MyNumber().Sum(1,2));
    }
}
```

注意，**包名必须和路径名字一样**，因为`javac`编译会根据 编译的当前路径开始，根据包名到对应的目录下去寻找文件进行编译为`.class`字节码

```bash
$ javac cloud/lyer/*.java #之后就会在对应目录下生成class文件
$ tree
cloud
└── lyer
    ├── App.class
    ├── App.java
    ├── MyNumber.class
    └── MyNumber.java
---------------------------------------
$ java cloud/lyer/App #执行即可
```

当然也可以编译到指定路径下，执行必须到类路径的顶部去执行，运维jvm转载类的时候会根据包名并且以命令执行的路径开始，到相对应路径下去装载，如果在其他层级路径则会提示类找不到

```bash
$ javac ./**/*.java -d ~/tmp/myApp
$ cd ~/tmp/myApp
$ java cloud/lyer/App #必须到对应路径下去执行
```

**一般操作是将所有java文件编译到指定目录比如build目录下，然后跑到build目录下去执行代码**

​    

## jar包

`jar`包是以 **ZIP** 格式来压缩的，目的是为了压缩Java字节码和代码等文件，方便代码的传输，同时也将凌乱的代码统一打包为一个`.jar`文件，方便携带和执行

- 压缩代码
- 方便携带和传输
- 方便执行

我们需要将编译好的`.class`文件打包为`jar`包

```bash
$ jar cvf App.jar ./cloud #c:创建 v:详细信息 f:指定新创建的文件名 
```

打包之后也可以通过jar包来运行里面的代码了，而不需要解压

```bash
$ java -cp App.jar cloud.lyer.App #-cp指定classpath java就会到此路径下去寻找字节码文件
```

也可以在jar包里的 `META-INF/MANIFEST.MF` 中的`Main-Class`，这样的话就可以直接运行jar包了，**注意MF文件不能有空行**

```bash
Manifest-Version: 1.0
Created-By: 1.7.0_06 (Oracle Corporation)
Main-Class: cloud.lyer.App
-------------------------------------
$ java -jar hello.jar #可以成功运行 和指定classpath功能一样
```

如果自己的jar包依赖的其他的jar包或则其他的库，则需要在运行的时候指定`classpath` 不指定的话默认会在当前目录下**根据包名** 到对应的路径下寻找

除非将其他库的jar包里面的字节码文件结合自己的代码重新再打一个jar包，否则就必须每次指定`classpath`

如下，App依赖了AB.jar里面的类，那么在执行的时候就必须指定`classpath`为AB.jar和当前路径，那么JVM就会根据包名到两个路径下去寻找字节码文件了

```bash
── AB.jar
└── cloud
    └── lyer
        ├── App.class
-----------------------------
$ java -cp AB.jar:. cloud.lyer.App
```

如果依赖的jar包很多，那么如果一个个指定估计要疯 我们可以直接自定一个库文件的目录，也就是将该目录下的所有文件都列入`classpath`路径的意思    

```bash
$ java -Djava.ext.dirs=~/lib cloud.lyer.App
```

​    

