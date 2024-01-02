---
title: 
aliases: 
- 委托
type: concepts
tags: 
- CSharp
---

## 关于委托

委托是[[reference type (csharp)|引用类型]]的一种，表示对具有特定参数列表和返回类型的方法的引用。它使用关键字 `delegate` 声明。

委托用于将方法作为参数传递给其他方法。它类似于 C++的[[function pointer (cpp)|函数指针]]，但它面向对象、 [[type-safe (csharp)|类型安全]]，且更可靠。因为这个特性，委托成了定义[[callback|回调]]方法的绝佳选择。

将委托与命名方法或匿名方法关联就可以实例化委托。在实例化委托时，只要某个方法的签名[^1]与委托*兼容*（不必完全匹配），它都可以关联到这个委托实例上。然后，就可以通过委托实例激活 (invoke) ——换个说法，调用 (call) ——这些方法。

```csharp
// An example of create and use delegate
// Create a method for a delegate.
public static void DelegateMethod(string message)
{
    Console.WriteLine(message);
}

// Instantiate the delegate.
Callback handler = DelegateMethod;

// Call the delegate.
handler("Hello World");
```

委托是[[event (csharp)|事件]]的基础（Delegates are the basis for [[event (csharp)|Events]] ( https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/events/ )）。

.NET 中提供了 [[System.Action]] 和 [[System.Func]] 两种类型，为许多常见委托提供了通用实现。

如果需要以不安全的方式调用方法，或者需要更进一步地控制调用，可以使用[[function pointer (csharp)|函数指针]]。

## Reference Links

- [Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/?source=recommendations)
- [Using Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/using-delegates?source=recommendations)
- [Built-in reference types - C# reference - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/builtin-types/reference-types#the-delegate-type)

## See also

- [When to Use Delegates Instead of Interfaces (C# Programming Guide) | Microsoft Learn]( https://learn.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2010/ms173173 (v=vs. 100))
- [Using Variance in Delegates (C#) - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates)
- [Delegates with Named vs. Anonymous Methods - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/delegates-with-named-vs-anonymous-methods)
- [How to combine delegates (Multicast Delegates) - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/how-to-combine-delegates-multicast-delegates)
- [How to declare, instantiate, and use a delegate - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/how-to-declare-instantiate-and-use-a-delegate)
- [Chapter 9. Delegates, Events, and Lambda Expressions | Microsoft Learn]( https://learn.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2008/ff518994 (v=orm. 10))
- [Chapter 17. Delegates and Events | Microsoft Learn]( https://learn.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2008/ff652490 (v=orm. 10))


[^1]: 对于委托，方法的签名包括访问级别（public、protected 等）+可选修饰符+返回值+方法名（+方法参数）。