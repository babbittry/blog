---
title: C语言学习笔记-数组
date: 2020-10-31 14:51:21
tags: 
- C语言
- 语言学习
---

数组：
1. 所有元素得存储空间是连续的

2. 所有元素的数据类型是一样的

3. 所有元素的所占的字节大小必须相等

<!-- more -->

数组只有在定义数组的同时才可以整体赋值，其他情况下整体赋值都是错误的。

例如下面是错误写法：

```C
int a[5];
a = {0, 1, 2, 3, 4}
```

数组的元素不是代表的这几个元素，而是代表的第一个元素的地址。

