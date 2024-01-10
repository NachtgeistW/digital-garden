---
title: 
aliases: 
- 委托
type: concepts
tags: 
- CSharp
draft: true
---

## 关于委托

委托是[[reference type (csharp)|引用类型]]的一种，表示对具有特定参数列表和返回类型的方法的引用。它使用关键字 `delegate` 声明。

委托用于将方法作为参数传递给其他方法。它类似于 C++的[[function pointer (cpp)|函数指针]]，但它面向对象、 [[type-safe (csharp)|类型安全]]，且更可靠。因为这个特性，委托成了定义[[callback|回调]]方法的绝佳选择。

将委托与命名方法或匿名方法关联就可以实例化委托。在实例化委托时，只要某个方法的签名[^1]与委托*兼容*（不必完全匹配），它都可以关联到这个委托实例上。然后，就可以通过委托实例激活 (invoke) ——换个说法，调用 (call) ——这些方法。

```csharp
// Declare a delegate.
delegate void NotifyCallback(string str);

// Declare a method with the same signature as the delegate.
static void Notify(string name)
{
    Console.WriteLine($"Notification received for: {name}");
}
```

委托是[[event (csharp)|事件]]的基础（Delegates are the basis for [[event (csharp)|Events]] ( https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/events/ )）。

.NET 中提供了 `System.Action` 和 `System.Func` 两种类型，为许多常见委托提供了通用实现。

如果需要以不安全的方式调用方法，或者需要更进一步地控制调用，可以使用[[function pointer (csharp)|函数指针]]。

### 属性

- 委托类似于 C++ 的函数指针，但委托完全面向对象，不像 C++ 指针会记住函数，委托会同时封装对象实例和方法。
- 委托允许将方法作为参数进行传递。
- 委托可用于定义回调方法。
- 委托可以链接在一起；例如，通过一个事件就能调用多个方法。
- 方法不必与委托类型完全匹配。 有关详细信息，请参阅[使用委托中的变体](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates)。
- 使用 [[lambda 表达式]]可以更简练地编写内联代码块。 Lambda 表达式（在某些上下文中）可编译为委托类型。 

## 定义与使用

委托的类型由委托的名称决定。下例定义了一个名为 `Callback` 的委托，该委托可以封装采用 `string`  作为参数并返回 `void` 的方法：

```csharp
public delegate void Callback(string message);
```

委托类型派生自 .NET 中的 [Delegate](https://learn.microsoft.com/zh-cn/dotnet/api/system.delegate) 类。委托类型是[[sealed (csharp)|密封的]]，它们不能从 `Delegate` 类中派生，也不能从其派生出自定义类。

委托对象通常可采用两种方式进行构造：将待封装方法的名称提供给委托；使用 lambda 表达式。实例化委托后就可以激活 (invoke) 它。激活委托会调用 (call) 附加到委托实例的方法。调用方传递给委托的参数将传递给所有的方法，并且委托会将方法的返回值（如果有）返回给调用方。例如：

```csharp
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

### 将委托自身作为参数传递

C# 中的[[callback#C 中的回调|回调]]多用委托实现。

### 还没想好这节叫啥

当构造委托时封装的是实例方法时，委托将同时引用实例和方法。委托不知道除其所封装方法以外的实例类型，因此委托可以引用任何类型的对象，只要该对象上有与委托签名匹配的方法。当构造委托时封装的是静态方法时，委托仅引用方法。请考虑以下声明：

```csharp
public class MethodClass
{
    public void Method1(string message) { }
    public void Method2(string message) { }
}
```

加上之前所示的静态 `DelegateMethod`，我们现在已有三个可由 `Del` 实例封装的方法。

### 多播

调用时，委托可以调用多个方法。这被称为[[multicasting|多播]]。若要向委托的方法列表（调用列表）添加其他方法，只需使用加法运算符或加法赋值运算符（`+` 或 `+=`）添加两个委托。例如：

``` csharp
var obj = new MethodClass();
Callback d1 = obj.Method1;
Callback d2 = obj.Method2;
Callback d3 = DelegateMethod;

//Both types of assignment are valid.
Callback allMethodsDelegate = d1 + d2;
allMethodsDelegate += d3;
```

此时，`allMethodsDelegate` 的调用列表中包含三个方法，分别为 `Method1`、`Method2` 和 `DelegateMethod`。原有的三个委托（`d1`、`d2` 和 `d3`）保持不变。调用 `allMethodsDelegate` 时，将按顺序调用所有三个方法。如果委托使用引用参数，引用将按相反的顺序传递到所有这三个方法，并且一种方法进行的任何更改都将在另一种方法上见到。当方法引发未在方法内捕获到的异常时，该异常将传递到委托的调用方，并且不会调用调用列表中的后续方法。如果委托具有返回值和/或输出参数，它将返回上次调用方法的返回值和参数。若要删除调用列表中的方法，请使用[减法运算符或减法赋值运算符](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/operators/subtraction-operator)（`-` 或 `-=`）。例如：

```csharp
//remove Method1
allMethodsDelegate -= d1;

// copy AllMethodsDelegate while removing d2
Callback oneMethodDelegate = allMethodsDelegate - d2;
```

由于委托类型派生自 `System.Delegate`，因此可以在委托上调用该类定义的方法和属性。例如，若要查询委托调用列表中方法的数量，你可以编写：

```csharp
int invocationCount = d1.GetInvocationList().GetLength(0);
```

调用列表中具有多个方法的委托派生自 [MulticastDelegate](https://learn.microsoft.com/zh-cn/dotnet/api/system.multicastdelegate)，该类属于 `System.Delegate` 的子类。由于这两个类都支持 `GetInvocationList`，因此在其他情况下，上述代码也将产生作用。

多播委托广泛用于事件处理中。事件源对象将事件通知发送到已注册接收该事件的接收方对象。若要注册一个事件，接收方需要创建用于处理该事件的方法，然后为该方法创建委托并将委托传递到事件源。事件发生时，源调用委托。然后，委托将对接收方调用事件处理方法，从而提供事件数据。给定事件的委托类型由事件源确定。有关详细信息，请参阅[[event (csharp)|事件]]。

### 比较委托

在编译时比较分配的两个不同类型的委托将导致编译错误。如果委托实例是静态的 `System.Delegate` 类型，则允许比较，但在运行时将返回 false。例如：

```csharp
delegate void Callback1();
delegate void Callback2();

static void method(Callback1 d, Callback2 e, System.Delegate f)
{
    // Compile-time error.
    //Console.WriteLine(d == e);

    // OK at compile-time. False if the run-time type of f
    // is not the same as that of d.
    Console.WriteLine(d == f);
}
```


## Reference Links

- [Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/?source=recommendations)
- [Using Delegates - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/using-delegates?source=recommendations)
- [Built-in reference types - C# reference - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/builtin-types/reference-types#the-delegate-type)
- [Using Variance in Delegates (C#) - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates)
- [Delegates with Named vs. Anonymous Methods - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/delegates-with-named-vs-anonymous-methods)
- [How to combine delegates (Multicast Delegates) - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/how-to-combine-delegates-multicast-delegates)
- [How to declare, instantiate, and use a delegate - C# Programming Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/delegates/how-to-declare-instantiate-and-use-a-delegate)

## See also

- [Chapter 9. Delegates, Events, and Lambda Expressions | Microsoft Learn]( https://learn.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2008/ff518994 (v=orm. 10))
- [Chapter 17. Delegates and Events | Microsoft Learn]( https://learn.microsoft.com/zh-cn/previous-versions/visualstudio/visual-studio-2008/ff652490 (v=orm. 10))
- [When to Use Delegates Instead of Interfaces (C# Programming Guide) | Microsoft Learn](https://learn.microsoft.com/en-us/previous-versions/visualstudio/visual-studio-2010/ms173173(v=vs.100))

[^1]: 对于委托，方法的签名包括访问级别（public、protected 等）+可选修饰符+返回值+方法名（+方法参数）。