---
title: 记一次 C# StackOverflowException 异常
date: 2021-07-18 16:14:22
tags: 
- C# 
- BUG
- 语言学习
---

## 背景

在看[刘铁猛老师的《C# 语言入门详解》的第17讲](https://www.bilibili.com/video/BV13b411b7Ht?p=17)的时候，在 45:00 处，学习属性的 `Get/Set` 函数，和老师一样的代码，但是老师的没有出错，我的程序在运行的时候却报出了

```
System.StackOverflowException:“Exception_WasThrown”
```

错误。

<!-- more -->

涉及到的代码如下：

```C#
public int Score       // 创建一个属性
{
    get
    {
        return Score;
    }
    set
    {
        if(value >= 0 && value <= 100)
        {
            Score = value;
        }
        else
        {
            throw new Exception("输入的分数不对！");
        }
    }
}
```



## 错误原因分析

经过查询得知，这个错误的原因是当使用`set()`设置 `Score` 属性的时候，会循环调用 `set()` 函数， `set()` 函数循环调用 `set()` 函数... 最终导致堆栈溢出。属性值获取的 `get` 方法，返回的是属性值本身，这属于递归循环了，导致堆栈溢出。[Stack Overflow](https://stackoverflow.com/questions/37596282/exception-of-type-system-stackoverflowexception-was-thrown) 上的原文如下：

>  When setting the price property, you invoke the setter, which invokes the setter which invokes the setter, etc..

## 解决办法

### 方法一：设置一个 `private` 属性名称相近的字段（field）来用于存储

废话不多说，直接看代码：

```C#
private int _Score;     // 字段
public int Score        // 属性
{
    get
    {
        return _Score;
    }
    set
    {
        if(value >= 0 && value <= 100)
        {
            _Score = value;
        }
        else
        {
            throw new Exception("输入的分数不对！");
        }
    }
}
```

这里定义一个私有的字段存储属性值，`get` 方法返回私有的值。属性没有存储数据的功能，数据都存在字段中，所以只有修改字段的数据才能更改数据，修改属性的值没用。

#### 属性的作用：

- 属性更灵活
- 属性是一种方法
- 属性实际是用来给字段赋值的，如果愿意的话。

### 方法二：直接设置 `{ get; set; }`

代码如下：

```C#
public int Score { get; set; } // 可以正常使用，不会报错
```

## 总结

方法一感觉比较繁琐，但是方法二不能在接收值的时候进行初步的判断，有可能会接收到非法的值（不合常理的值，比如年龄200岁等）。方法二是 `C#` 的语法糖。

最终重新看视频中的代码发现，老师除了声明这个属性之外，还声明了一个字段，但是首字母是小写的。所以属于看错了，但是也因此加深了一下对字段（field）和属性（property）的理解。
