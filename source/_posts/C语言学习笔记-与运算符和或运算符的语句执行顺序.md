---
title: C语言学习笔记-与运算符和或运算符的语句执行顺序
date: 2021-11-05 10:11:38
tags: 
- C语言
- 语言学习
- BUG
---

## 与运算符和或运算符的语句执行顺序

有一个老生常谈的问题，那就是与运算符和或运算符的语句执行顺序。

```C
express1 || express2
```

先执行表达式`express1`如果为“真”，`express2`将不被执行，`express2`仅在`express1`为“假”时才被执行。因为第一个表达式为真了，整个表达式都为真，所以没有必要再去执行第二个表达式了。

```C
express1 && express2
```

先执行表达式`express1`如果为“假”，`express2`将不被执行，`express2`仅在`express1`为“真”时才被执行。因为第一个表达式为假了，整个表达式都为假了，所以没有必要再去执行第二个表达式了。

于是，他并不是你所想像的所有的表达式都会去执行，这点一定要明白，不然你的程序会出现一些莫明的运行时错误。

<!-- more -->

例如，下面的程序：

```C
if (sum > 100 && (fp=fopen( filename,"a") != NULL )
{
    fprintf(fp, "Warring: it beyond one hundred/n");
    ......
}

fprintf( fp, " sum is %id /n", sum );
fclose( fp );
```

本来的意图是，如果`sum > 100`，向文件中写一条出错信息，为了方便，把两个条件判断写在一起，于是，如果`sum<=100`时，打开文件的操作将不会做，最后，`fprintf`和`fclose`就会发现未知的结果。