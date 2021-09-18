---
title: Llinux 执行命令为什么要加 ./（点斜杠） ？
date: 2021-09-17 21:25:18
tags: Linux
comments: true
---

## 问题引入

如下图所示，当前文件夹下有一个名为 `Hello.sh` 的 shell 脚本，但是在执行它的时候却提示找不到文件，只有当前面加上 `./` 才可以正确的运行这个脚本。那么这个是为什么呢？

![Linux bash dot slash](https://gitee.com/babbittry321/blogImages/raw/master/img/Linux%20bash%20dot%20slash.png)

<!-- more -->

## 原因分析

虽然我们的终端是在当前目录，而且当前目录中有我们要执行的文件，但是终端就是找不到它。需要我们指定文件的相对或绝对路径来告诉终端我们想运行的文件在哪里。

在 Linux 中，点字符（`.`）表示工作目录，斜杠（`/`） 是 Linux 中的路径分隔符，我们用它来分离工作目录（`.`）与文件名。

为什么我们需要 `./` ？因为我们的工作目录不包含在环境变量（ `$PATH` ）中。

## 什么是环境变量

`$PATH`：决定了shell将到哪些目录中寻找命令或程序，`$PATH`的值是一系列目录，当你运行一个程序时，Linux 在这些目录下进行搜索。

输入命令 `echo $PATH` 就可以查看当前的环境变量是多少。

在我的电脑上输出是（不同电脑输出不一样）：

```
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

![echo path](https://gitee.com/babbittry321/blogImages/raw/master/img/echo%20path.png)

我们在 Linux 上使用的命令都可以在这里面找到。如果 `ls` 命令。如果我们想看命令具体的位置，可以使用 `which` 命令（感觉有点像套娃标😥）

![which ls](https://gitee.com/babbittry321/blogImages/raw/master/img/which%20ls.png)

## 终端中命令搜索规则

- 如果在路径名中存在 `./` 字符，那么就不搜索 `$PATH`。
- 如果 `./` 不在路径名中，那么只在 `$PATH` 中搜索。

所以这就解释了为什么如果在 `Hello.sh` 前不加 `./` 就不能正常运行，原因就是在环境变量中没有名为 `Hello.sh` 的命令！

## 把自己的命令添加进环境变量

如果自己写了几个命令，并且以后在运行的时候不想添加路径，那么可以把自己的命令所在路径添加进环境变量。

比如我写了两个命令，分别是 `my_ls.sh` 和 `Hello.sh`，然后把他们放在了 `~/Desktop/myPATH` 中。

![myPATH](https://gitee.com/babbittry321/blogImages/raw/master/img/mtPATH.png)

在终端中输入：`export PATH=$PATH:/home/zxy/Desktop/myPATH`（这是一次临时的指定方法，关闭终端之后就不再生效，这里只是演示效果，如果想了解更多，可以搜索“Linux 环境变量”）

![添加环境变量](https://gitee.com/babbittry321/blogImages/raw/master/img/PATH.png)

然后执行 `Hello.sh`，就可以看到输出结果。

![my option](https://gitee.com/babbittry321/blogImages/raw/master/img/my%20Hello.sh.png)

这里提示 `my_ls.sh` 的权限不够，那么就提升一下权限。

![提升权限](https://gitee.com/babbittry321/blogImages/raw/master/img/chmod.png)

这样就可以，在不输入 `./` 的情况下直接运行 `Hello.sh`。

## 扩展：Linux中的`~`、`/`、`./`分别代表什么

`~`表示主目录，也就是当前登录用户的用户目录。

比如：我登录用户是chen,`~` 代表的就是`/home/chen`。

`/`是指根目录：就是所有目录最顶层的目录。

`./`表示当前目录，`./` 一般需要和其他文件夹或者文件结合使用，指代当前目录下的东西。

`..`表示上级目录，`../` 指代上级目录下的东西。



