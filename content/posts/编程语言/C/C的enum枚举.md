---
title: C的enum枚举
date: 2021-05-04
categories: [编程语言/C]
tags: [C]
---

## enum枚举

如果没有枚举的话，我们其实也可以用宏定义来实现，只是代码不够优雅

```c
#include <stdio.h>
enum DAY{
    MON,THU,WED
};

int main(int argc, char const *argv[])
{
    enum DAY day=THU;
    printf("%d\n",day); //1
    return 0;
}
```