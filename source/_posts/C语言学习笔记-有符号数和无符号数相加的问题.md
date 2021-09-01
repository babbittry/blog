---
title: C语言学习笔记-有符号数和无符号数相加的问题
date: 2021-09-01 11:26:05
tags: 
- C语言
- 语言学习
---

### Q: 下面的代码输出是什么，为什么？ 

```C
void test(void)
{
    unsigned int a = 6;
    int b = -10;
    ((a + b) > 0) ? printf("a + b > 0\r\n") : printf("a + b < 0\r\n");
    printf("a + b = %d\r\n", a + b);
}
```

### A: 

废话，输出是：

```
a + b < 0
a + b = -4
```

---

**但是，上机实际跑一跑，真的是这样的吗？**

<!-- more -->

编写程序如下：

```C
#include <stdio.h>
#include <stdlib.h>

void test(void) {
    unsigned int a = 6;
    int b = -10;
    ((a + b) > 0) ? printf("a + b > 0\r\n") : printf("a + b < 0\r\n");
    printf("a + b = %d\r\n", a + b);
    printf("b 的整形：%d\r\n", b);
    printf("b 的无符号整形：%u\r\n", b);
    printf("b 的十六进制表达：%x\r\n", b);
}

int main(void) 
{
    test();
    printf("INT_MAX:%d\r\n", INT_MAX);
    printf("INT_MIN:%d\r\n", INT_MIN);
    printf("UINT_MAX:%u\r\n", UINT_MAX);
    system("pause");
    return 0;
}
```

程序运行完成后的输出如下：

```
a + b > 0
a + b = -4
b 的整型：-10
b 的无符号整型：4294967286
b 的十六进制表达：fffffff6
INT_MAX:2147483647
INT_MIN:-2147483648
UINT_MAX:4294967295
```

## 为什么会有这样的结果？

这个问题测试你是否懂得 C 语言中的整数自动转换原则，原因是当表达式中存在有符号类型和无符号类型时，所有的操作数都自动转换为无符号类型。因此 `-10` 变成了一个非常大的正整数，所以程序输出的结果是 `a + b > 0`。这一点对于应当频繁用到无符号数据类型的嵌入式系统来说是丰常重要的。

那么 `-10` 究竟被转换为了几呢？

如下图所示，在程序中打断点，在左侧的监视区可以看到，此时 `a` 的值为 `6` ，`b` 的值为 `-10 `，但是 `a + b` 的值为 `4294967292`。

![变量监控](https://gitee.com/babbittry321/blogImages/raw/master/img/uint%20add%20int.png)

所以此时 `b` 的值为 `4294967286`，它等于 `4294967295 - 9`，从上面的输出的结果可以验证，有符号类型的 `b` 被自动转换为无符号类型.

## 扩展：`int`的最大值和最小值和`uint`的最大值

在 `limits.h` 这个文件中，有如下的宏定义：

```C
#define INT_MIN     (-2147483647 - 1)
#define INT_MAX       2147483647
#define UINT_MAX      0xffffffff
```

