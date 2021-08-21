---
title: C语言学习笔记-浮点数的使用注意事项
date: 2021-08-20 22:06:28
tags: 
- C语言
- 语言学习
---

## 浮点数都是有符号的，没有 `unsigned` 浮点数

### Q: 

为什么没有无符号的浮点型，比如 `unsigned float` 或 `unsigned double`？

### A: 

为什么 `C/C++` 不支持无符号浮点数是因为CPU没有等效的机器代码操作来执行。因此支持它是非常低效的。

<!-- more -->

所以问题就是为什么硬件实施者不支持它？我认为答案是最初没有定义无符号浮点标准。由于语言喜欢向后兼容，即使添加了语言也无法使用它。要查看浮点规范，您应该查看[IEEE标准754浮点数](http://steve.hollasch.net/cgindex/coding/ieeefloat.html)。

我肯定无符号浮点数是有用处的。但是 `C/C++` 倾向于选择效率最高的安全性。

## C 语言两个浮点数比较大小的办法

浮点数并非真正意义上的实数，只是其在某个范围内的近似。

因此两个浮点数比较大小时，不能简单地使用大于小于号进行比较，应该判断连个浮点数差值的绝对值是否近似为0。

```C
#include <stdio.h>
#include <math.h>
 
// 这里的 EPS 是自己定义的精度
#define EPS 1e-7     // 判断浮点数是否位于0的一个很小的邻域内[-EPS,EPS]内

int main(void)
{
    /* 判断一个浮点数是否等于 0*/
    float a;
    scanf("%f", &a);
    if(fabs(a) <= EPS)  // a=0
        ...
    else if(a > EPS)    // a>0
        ...
    else                // a<0
        ...
 
    /* 比较两个浮点数大小 */
    float a,b;
    scanf("%f%f",&a, &b);
    if(fabs(a-b) <= EPS)  // a=b
        ...
    else if( (a-b) > EPS)  // a>b
        ...
    else                   // a<b
        ...
    
}
```



