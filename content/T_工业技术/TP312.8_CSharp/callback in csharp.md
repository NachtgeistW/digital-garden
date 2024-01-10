---
title: C# 中的回调
aliases: 
- C# 中的回调
type: concepts
tags: 
---

> I just met you,  
> And this is crazy,  
> But here's my number (delegate),  
> So if something happens (event),  
> Call me, maybe (callback)?
> 
> [c# - What is a callback? - Stack Overflow](https://stackoverflow.com/questions/2139812/what-is-a-callback/13128949#13128949)

C# 中的回调用[[delegate (csharp)|委托]]实现；通常配合[[event (csharp)|事件]]一起使用，因为事件能自动激活附加在它上的委托（event handlers）。

## 将回调作为自定义排序方法

```csharp
public delegate void Callback(string message);
public static void DelegateMethod(string message) 
	=> Console.WriteLine(message);
Callback handler = DelegateMethod;

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

以抽象方式使用委托时，`MethodWithCallback` 不需要直接调用控制台——它在设计时根本不必考虑控制台。它只是简单地准备了字符串并将字符串传递给其他方法。由于委托化的方法可以使用任意数量的参数，此功能特别强大。

## Reference Links

- [Using Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/using-delegates)
