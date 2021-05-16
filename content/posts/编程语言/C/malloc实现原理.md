---
title: malloc实现原理
date: 2021-05-01
categories: [编程语言/C]
tags: [C]
draft: true
---

## malloc和calloc的区别

`malloc`分配的内存不会进行初始化，有可能内存里还包含其他脏值。而`calloc`分配的内存会将内存值都初始化为`0`，并且`calloc`分配数组类型的内存更加方便，传入类型大小以及分配的个数即可

malloc案例

```c
//malloc
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    char *p=NULL;
    p = (char*)malloc(sizeof(50));
    if(p==NULL){
        perror("分配失败");
        exit(EXIT_FAILURE);
    }
    for(int i=0;i<50;i++){
        printf("%d\n",*p++); //有可能会有非0值 也就是脏值
    }
    return 0;
}
```

calloc案例

```c
//calloc
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char const *argv[])
{
    char *p=NULL;
    p = (char*)calloc(50,1); //分配大小为1byte的50个连续的位置
    if(p==NULL){
        perror("分配失败");
        exit(EXIT_FAILURE);
    }
    for(int i=0;i<50;i++){
        printf("%d\n",*p++); //都是0
    }
    return 0;
}
```

​    

## 参考

[malloc和free的实现原理解析](https://jacktang816.github.io/post/mallocandfree/)