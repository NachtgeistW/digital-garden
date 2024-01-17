---
aliases: [default lambda parameters, 默认 lambda 参数]
type: concepts
tags:
  - CSharp/CSharp12
---

从 C# 12 开始，你可以在 [[lambda 表达式]]里声明默认参数了：

```csharp
var IncrementBy = (int source, int increment = 1) => source + increment;

Console.WriteLine(IncrementBy(5)); // 6
Console.WriteLine(IncrementBy(5, 2)); // 7
```

默认的 lambda 参数让代码调用可以略过传参，并在不破坏原本的代码调用的情况下，往已有的 lambda 表达式中添加参数。无需再通过重载或空检查来处理可选参数。这种简化的 lambda 表达式访问与普通方法的默认参数一样，以相同的形式简化了代码调用。

# Reference Links
- [Announcing C# 12 - .NET Blog (microsoft.com)](https://devblogs.microsoft.com/dotnet/announcing-csharp-12/#default-lambda-parameters)
- [more about default lambda parameters in this article](https://learn.microsoft.com/dotnet/csharp/whats-new/csharp-12#default-lambda-parameters)