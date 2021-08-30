---
title: C语言学习笔记-宏定义的使用注意事项
date: 2021-08-30 22:20:14
tags: 
- C语言
- 语言学习
---

## 在使用宏定义的时候有一些注意事项

必须注意，要适当的使用圆括号以保证计算次序的正确性。

比如：

```C
# define square(x) x * x
```

当使用 `square(x + 1)` 调用该宏定义的时候会出现什么情况呢？

<!-- more -->

请见下面例子：

```C
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

# define square(x) (x * x)

int main(void) 
{
    int i = 2;
    printf("%d * %d = %d\r\n", i, i, square(i));
    printf("%d * %d = %d\r\n", i + 1, i + 1, square(i + 1));
    system("pause");
    return 0;
}
```

运行这段函数得到的输出为：

```
2 * 2 = 4
3 * 3 = 5
```

究其原因，就是在执行 

```C
printf("%d * %d = %d\r\n", i + 1, i + 1, square(i + 1))
```

的时候，系统在预编译之后，把这行代码替换成了：

```C
printf("%d * %d = %d\r\n", i + 1, i + 1, i + 1 * i + 1)
```

所以才导致的错误。

所以用宏定义时，含参数时，参数本身要加`()`，此外，对参数的操作整体也要加`()`。

同时在使用宏的时候，要充分考虑前后的语境，必要的时候亲自做一下代换试试，防止出现不必要的错误。
