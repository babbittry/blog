---
title: ARM-linux 嵌入式板子移植 lrzsz
date: 2021-03-21 21:45:18
tags: Linux
comments: true
---

因为无法在公司内部搭建局域网，或者开发板没有网口，并且需要在windows和ARM板之间传输文件，这时就可以选择使用lrzsz，它可以将windows的文件通过串口传输到ARM板上。将lrzsz 安装到 ARM-linux 嵌入式板子上需要移植，但网上查到的资料很乱，有的讲述不清楚、甚至还有错误，所以自己写一个详细的记录，便于自己以后查看，也希望能帮助其他人。

<!-- more -->

## 下载源码

首先下载最新版的 lrzsz， [**点我开始下载**](https://ohse.de/uwe/software/lrzsz.html)。下面以 0.12.20 版本为例，下载后文件名为：lrzsz-0.12.20.tar.gz。

## 复制到 ubuntu 主机并解压

复制并解压源码包将下载的源码包复制到Linux主机，然后进行解压：
```shell
mkdir ~/lrzsz
cp lrzsz-0.12.20.tar.gz ~/lrzsz
cd ~/lrzsz
tar zxvf lrzsz-0.12.20.tar.gz
```

## 安装并使用交叉编译工具链

使用APT安装ARM-GCC安装交叉编译工具链有如下三种方式：

- 直接在Ubuntu下使用APT包管理工具下载安装，操作简单，我们直接使用该方式安装即可。
- 自行下载第三方制作好的工具链，如Linaro，好处是选择丰富，能找到很多不同的版本。
- 使用crosstool-ng根据需要自己制作，过程复杂，不推荐。

我们直接在Ubuntu系统下使用APT包管理工具安装。使用的编译器主要有两种类型：

- arm-linux-gnueabihf-gcc：名称中的Linux表示目标应用程序是运行在Linux操作系统之上的，例如前面的hello.c程序。
- arm-none-eabi-gcc，名称中的none表示无操作系统，目标应用程序的运行环境是不带操作系统的，例如裸机代码、uboot、内核代码本身。

不过在开发中比较多的开发者对所有程序都直接用arm-linux-gnueabihf-gcc来编译，包括裸机代码和uboot，虽然可能因为代码本身没有调用到Linux相关的内容而不会出错，但这样做不够严谨，条件允许的话，我们还是严格区分开来。在编译 lrzsz 的时候只需要使用arm-linux-gnueabihf-gcc编译器，可通过APT包管理工具可直接执行以下命令安装：

```shell
#在主机上执行如下命令
sudo apt install gcc-arm-linux-gnueabihf
```

```shell
#安装完成后使用如下命令查看版本
arm-linux-gnueabihf-gcc -v  或 arm-linux-gnueabihf-gcc --version
```

可以看到下图的内容，它表明交叉编译工具链安装成功了，输出信息中的 “Target: arm-linux-gnueabihf”表明了它的目标架构。
![](https://gitee.com/babbittry321/blogImages/raw/master/img/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20210306203223.png)

安装完成后输入`arm-linux-gnueabihf-`，再按两下TAB键，终端会提示可用的相关命令，如下图包含了ARM-GCC工具链Binutils的各种工具。![安装后包含的Binutils工具集](https://gitee.com/babbittry321/blogImages/raw/master/img/Image.png)


## 交叉编译(需要提前装好交叉编译环境)

### 配置

 ```shell
CFLAGS=-O2 CC=arm-linux-gnueabi-gcc ./configure --cache-file=arml_cachefile0 --prefix=/usr/local/
 ```
这里 ./configure 必须放在后面，否则无法识别到交叉编译工具。 

### 编译

编译命令：

```shell
# 并行编译，使用4个CPU同时编译，可提高编译速度
make -j4                              
```

make完成之后，会在当前目录 src/ 下生成 lrz 和 lsz 两个文件。 

## 安装到开发板

通过网络、U盘、SD卡等方式，将生成的 lrz 和 lsz  复制到目标板的 /usr/bin 目录，并在目标版上添加可执行权限。

### 挂载优盘


1. 以root用户登陆
2. 查看当前挂载
    ```shell
    fdisk -l
    ```
    一般情况未挂载的硬盘都在最后，以/dev/sdb1为例。
3. 新建一个目录来挂载硬盘，挂载到mnt/usb
    ```shell
    cd /mnt
    mkdir /mnt/usb
    ```
4. 挂载
    ```shell
    mount /dev/sdb1 /mnt/usb
    ```
5. 卸载u盘：在使用完u盘后，在拔出前需要先键入卸载U盘命令:
    ```shell
    umount /mnt/usb 
    ```

### 复制到开发板并安装

```shell
mount -t vfat /dev/sda1 /mnt/usb       # 挂载U盘
cp lrz lsz /usr/bin                            # 复制
cd /usr/bin/
chmod +x lrz lsz
```

至此安装完成。

## 使用 lrzsz 传输文件

**注意**：我用的串口软件是 mobaxterm，其它软件稍有区别。

### 执行 lrz 命令将文件从PC机传送到目标版上：

进入目标板的文件夹，然后输入`lrz`，mobaxterm界面会出现一堆乱码，然后右键选择“send file using Z-modem”
![](https://gitee.com/babbittry321/blogImages/raw/master/img/20210306211059.png)
然后在弹出的界面上选中文件，就可以将文件传输到目标版的目标文件夹。

**注意**：这里右键选择的和输入的命令正好相反。

下面以“/usr/src”为目标文件夹为例，命令如下：

```shell
cd /usr/src     # 进入目标文件夹
lrc                 # 输入命令后右键，选择“send file using Z-modem”，然后选择文件
```

### 执行 lsz 命令将文件从目标版传到PC机上：

以“/usr/src”文件夹里面的readme.txt为例，命令如下：

```shell
cd /usr/src     # 进入目标文件夹
lsz readme.txt      # 输入命令后右键，选择“receive file using Z-modem”，然后选择要保存的路径
```