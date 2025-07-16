---
title: 
aliases: 
- 回调
- 回调函数
type: concepts
tags: 
---

> 调用函数时顺便告诉函数“你本身执行完以后要做什么事情”，这个“执行完后做什么事情”就是回调函数。

回调函数（简称回调，Callback，即 call then back）指的是将一个函数 A 作为参数传递给另一个函数 B 并在 B 中执行 A，其中的函数 A 就是回调函数。回调既有同步的，又有[[asynchronous callback|异步]]的。这一设计允许底层代码调用在高层定义的子程序。

回调的常见用途：

- 将回调函数作为参数传给某个函数，并在稍后调用它。使用回调的函数不需要知道回调是如何实现的。该功能类似于封装好的[[interface (csharp)|接口]]提供的功能。这种回调一般是异步。
- 另一个常见用途是定义自定义比较方法并将该委托传递给一个排序方法。它允许调用方的代码成为这个排序算法的一部分。

## Reference Links

- [Using Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/using-delegates)

## See also

- [[callback in csharp|C# 中的回调]]