---
title: WPF 学习笔记-使用默认浏览器打开网页出现用户未处理的异常
date: 2021-11-30 12:56:21
tags: 
- C#
- 语言学习
- WPF
---

在用 WPF 做扫雷小游戏的时候，需要使用默认浏览器打开网页，但是从网上搜索来的答案都是下面这样的。

```C#
System.Diagnostics.Process.Start("www.bing.com");
```

但是在程序中这样写的时候，会发生如下错误：

![WPF open URL error](/images/WPF_open_URL_error.png)

<!-- more -->

（我当前的运行环境是 VS2022 + .Net6.0）

我在中文的搜索引擎上找了半天都没找到解决方法，最后在 [Stack Overflow](https://stackoverflow.com/)上找到了解决方案（源网址见下面的[参考资料](#参考资料)）。所以在这里记录下来，希望能帮到更多的人。

出现这样的原因是在 .Net Core 中，为了保证跨平台性，需要委托 `Windows Shell` 来实现的一些事情需要使用 `Process.StartUseShellExecute=true` 来显式的声明。

所以在 .Net Core 和 .Net 5、.Net 6 中应该使用如下方法：

```C#
var destinationurl = "www.bing.com";
var sInfo = new ProcessStartInfo(destinationurl)
{
    UseShellExecute = true,
};
Process.Start(sInfo);
```

## 参考资料

[System.Diagnostics.Process.Start · Issue #2566 · dotnet/wpf (github.com)](https://github.com/dotnet/wpf/issues/2566)

[c# - Open Browser with URL WPF - Stack Overflow](https://stackoverflow.com/questions/60181640/open-browser-with-url-wpf/60221582#60221582)

