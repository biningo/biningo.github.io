---
title: C的extern关键字
date: 2021-05-01
categories: [编程语言/C]
tags: [C]
---

## extern关键字

`extern`关键字的作用就是告诉编译器此值在其它文件中定义了，这里只是作个声明

```c
//sum.c
//表示a b的值由外部定义了
extern int a; 
extern int b;
int sum(){
    return a+b;
}

//main.c
#include <stdio.h>
extern int sum(); //表示sum是外部定义的 因为这里没有.h头文件
int a = 10;
int b = 20;
int main(int argc, char const *argv[])
{
    int c = sum();
    printf("%d\n", c); //30
    return 0;
}
```

再看一个案例

```c
//a.c
char a = 'A'; 

//main.c
int main(){
    extern char a;  //引用a.c中定义的全局变量
    printf("%c", a);
}
```

​    

## static关键字

此关键字修饰的全局变量表示禁止被外部的`extern`所引用，表示这个变量的作用域仅仅在此文件内

```bash
static int age=19;
```

比如我在上面的`a`前加一个`static`那么main中就无法引用了，就会报错

此关键字如果修饰函数则表示此函数不可以被外部调用

```c
static void hello(){
    printf("hello\n");
}
```

