---
title: 协变与逆变
aliases:
  - covariance
  - contravariance
  - 协变
  - 逆变
type: concepts
tags: 
  - CSharp
---

协变和逆变都是计算机科学里的术语。
- 协变指能够使用比原始指定的派生类型的派生程度更大（更具体的）的类型。若类型 A 为协变量，则需要使用类型 A 的地方可以使用 A 的某个子类类型。
- 逆变指能够使用比原始指定的派生类型的派生程度更小（不太具体的）的类型。若类型 A 为逆变量，则需要使用类型 A 的地方可以使用 A 的某个基类类型。

在 C# 里，协变与逆变作用于数组、[[delegate (csharp)|委托]] 和[[generic (csharp)|泛型]]接口的变量上，允许它们做隐式引用转换。

```csharp
IFoo<父类> = IFoo<子类>; //协变
IBar<子类> = IBar<父类>; //逆变
```

## 泛型接口与委托中的协变与逆变

从 .NET Framework 4 开始，C# 支持在泛型接口和委托中使用协变和逆变，并允许隐式转换泛型类型参数。

一个泛型接口的例子：

```csharp
IEnumerable<String> strings = new List<String>();  
IEnumerable<Object> objects = strings;
//注意：C# 里所有的类都继承自 Object 类型。属于 Object 子类的 String 类型接口能引用到 Object 接口上，这就是协变。
```

如果泛型接口或委托的泛型参数被声明为协变或逆变，该泛型接口或委托则被称为“[[变体]]”。对泛型类型参数使用 `out` 就会将其声明为协变；使用 `in` 就会将其声明为逆变。

## Reference Links

- [协变和逆变 (C#) | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/)
- [泛型中的协变和逆变 - .NET | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/standard/generics/covariance-and-contravariance)

## See also

- [.NET C#基础（8）：变体 - 协变、逆变与不变 - HiroMuraki - 博客园](https://www.cnblogs.com/HiroMuraki/p/16355137.html)
- [C# - 协变、逆变 看完这篇就懂了 - Virgil-Zhou - 博客园](https://www.cnblogs.com/VVStudy/p/11404300.html)