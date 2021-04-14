---
title: C语言学习笔记-break和continue
date: 2020-10-31 14:51:21
tags: 
- C语言
- 语言学习
---

在循环体中，C 提供了下列的循环控制语句。
- break
- continue
- goto

但是 `goto` 尽量不要使用。它会导致代码结构混乱。

<!-- more -->

```C
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int x = 5;
    int y = 5;

    while (x > 0)
    {
        printf("x为：%d\r\n", x);
        if (x <= 3)
        {
            break;
        }
        x--;
    }

    while (y > 0)
    {
        printf("y为：%d\r\n", y);
        y--;
        if (y <= 2)
        {
            continue;
        }
        printf("y为：%d\r\n", y + 1);
    }
    system("pause");
}

```

上面代码执行的结果为：

```
x为：5
x为：4
x为：3
y为：5
y为：5
y为：4
y为：4
y为：3
y为：2
y为：1
```

从结果可以看出来：

当`break`语句出现在一个循环内时，循环会立即终止，且程序流将继续执行紧接着循环的下一条语句。

而`continue`会跳过当前循环中的代码，强迫开始下一次循环。

在多层循环中，`break`只能终止距离它最近的循环。 

在多层`seitch`嵌套中，`break`只能终止距离它最近的`switch`。 

函数中的`return`与`break`比较像，`return`的含义是：

1. 终止被调函数，并像主调函数返回表达式的值
2. `break`是用来终止循环和`switch`的，`return`是用来终止函数的。



