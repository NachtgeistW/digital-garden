---
title: 
aliases: 
- 回调
- 回调函数
type: concepts
tags: 
draft: true
---

> 调用函数时顺便告诉函数“你本身执行完以后要做什么事情”，这个“执行完后做什么事情”就是回调函数。

在计算机程序设计中，回调函数，或简称回调（Callback，即 call then back，被主函数调用运算后会返回主函数），是对作为参数传递给另一段代码的可执行代码的任何引用；该代码预计会回调（执行）回调函数作为其工作的一部分。此执行可能是立即的，如在同步回调中，也可能在稍后的时间点发生，如在[[asynchronous callback|异步回调]]中。它们也称为阻塞和非阻塞。这一设计允许了底层代码调用在高层定义的子程序。

## C# 中的回调

> I just met you,  
> And this is crazy,  
> But here's my number (delegate),  
> So if something happens (event),  
> Call me, maybe (callback)?
> 
> [c# - What is a callback? - Stack Overflow](https://stackoverflow.com/questions/2139812/what-is-a-callback/13128949#13128949)

C# 中的回调用[[delegate (csharp)|委托]]实现；通常还会伴随大量的 [[event (csharp)|event]] ，因为 event 能自动激活附加在它上的委托（event handlers）。

C# has [delegates](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/index) for that purpose. They are heavily used with [events](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/events/), as an event can automatically invoke a number of attached delegates (event handlers).

由于实例化的委托是一个对象，因此可以作为实参传递或分配给一个属性。这允许方法接受委托作为参数并在稍后调用委托。这被称为[[asynchronous callback|异步回调]]，是在长进程完成时通知调用方的常用方法。当以这种方式使用委托时，使用委托的代码不需要知道要使用的实现方法。功能类似于封装接口提供的功能。

回调的另一个常见用途是定义自定义比较方法并将该委托传递给一个排序方法。它允许调用方的代码成为这个排序算法的一部分。以下示例方法使用 `Del` 类型作为参数：

```csharp
//定义
public static void MethodWithCallback(int param1, int param2, Callback callback)
{
    callback("The number is: " + (param1 + param2).ToString());
}

//传递上面写的 handler 委托
MethodWithCallback(1, 2, handler);

//控制台输出
The number is: 3
```

以抽象方式使用委托时，`MethodWithCallback` 不需要直接调用控制台，记住，其不必设计为具有控制台。 `MethodWithCallback` 的作用是简单准备字符串并将字符串传递到其他方法。由于委托的方法可以使用任意数量的参数，此功能特别强大。

## Reference Links

- 

## See also

- 