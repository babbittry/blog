---
title: i&ltj&ltk的C语言表达
date: 2020-11-03 20:01:20
tags:
- C语言
- 语言学习
---

表达式`i < j < k`在C语言中是合法的，但是它不是你所期望的意思。因为`＜`运算符是左结合的， 所以这个表达式等价于`(i < j) < k`.

换句话说， 表达式首先检测l.是否小于j, 然后用比较后产生的结果1或0来和K进行比较。 表达式不测试j是否位于i和K之间,正确的表达式应该是`i < j && j < k`.

<!-- more -->

```C
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int i = 2;
    int j = 1;
    int k = 3;
    if(i < j < k){      // 这里输出第一行，但是这是错的
        printf("i小于j，j小于k，j在i和k中间\r\n");
    }
    else{
        printf("j不在i和k中间\r\n");
    }
    if(i < j && j < k){      // 这里输出第二行，这样处理是对的
        printf("i小于j，j小于k，j在i和k中间\r\n");
    }
    else{
        printf("j不在i和k中间\r\n");
    }
    system("pause");
}
```

在`Python`中没有这样的问题，如下面程序：

```Python
#!/usr/bin/python
# coding=utf-8

i = 2;
j = 1;
k = 3;
if i < j < k:      # 这里输出第二行，可见Python进行了特殊处理
    print("i小于j，j小于k，j在i和k中间\r\n");
else:
    print("j不在i和k中间\r\n");
if i < j and j < k:      # 这里输出第二行，这样处理是对的
    print("i小于j，j小于k，j在i和k中间\r\n");
else:
    print("j不在i和k中间\r\n");
```

输出的结果为：

```
j不在i和k中间
j不在i和k中间
```