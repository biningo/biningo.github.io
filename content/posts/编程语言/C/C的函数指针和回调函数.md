---
title: C的函数指针和回调函数
date: 2021-05-04
categories: [编程语言/C]
tags: [C]
---

## 回调函数

下面将四则运算法则传入函数中进行回调

```c
int sum(int i1,int i2){
    return i1+i2;
}

int sub(int i1,int i2){
    return i1-i2;
}
int mul(int i1,int i2){
    return i1*i2;
}
int div(int i1,int i2){
    return i1/i2;
}
void func(int i1,int i2,int (*operate)(int,int)){
    printf("i1:%d i2:%d = %d\n",i1,i2,operate(i1,i2));
}

int main(int argc, char const *argv[])
{
    func(8,4,sum);
    func(8,4,sub);
    func(8,4,mul);
    func(8,4,div);
    return 0;
}
```

​    

## 函数指针用于结构体

函数指针可以用于结构体，实现类似于了类方法的效果

```c

typedef struct Stu
{
    char *name;
    void (*say)(char *msg);
}Stu;

void say(char *name){
    printf("hello,%s\n",name);
}

int main(int argc, char const *argv[])
{
    Stu stu;
    stu.name = "lyer";
    stu.say = &say;
    stu.say(stu.name);
    return 0;
}
```

