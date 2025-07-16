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

协变 (covariance) 的前缀 co- 是“共同”的意思，所以 `I<D>` 和 `I<B>` 的关系是跟 `D` 和 `B` 的关系一致的；逆变 (contravariance) 的前缀 contra-是“反”的意思，所以 `I<D>` 和 `I<B>` 的关系跟 `B` 和 `D` 相反。

在 C# 里，协变与逆变作用于数组、[[delegate (csharp)|委托]]和[[generic (csharp)|泛型]]接口的变量上，允许它们做隐式引用转换。

```csharp
IInterface<Derived> = IInterface<Base>; //协变
IInterface<Base> = IInterface<Derived>; //逆变
```

## 泛型接口与委托中的协变与逆变

从 .NET Framework 4 开始，C# 支持在泛型接口和委托中使用协变和逆变，并允许隐式转换泛型类型参数。

比如说， `IEnumerable<>` 是协变的，`IEnumerable<Derived>` 可以当成 `IEnumerable<Base>` 用。例子：

```csharp
IEnumerable<String> strings = new List<String>();  
IEnumerable<Object> objects = strings;
//注意：C# 里所有的类都继承自 Object 类型。在这个泛型集合接口 IEnumerable<T> 中，属于 Object 子类的 String 类型能引用到 Object 接口上，这就是协变。
```

一般用 `IEnumerable` 的时候，是从 `IEnumerable` 里面拿东西出来用。如果想要一个基类 `Base`，但拿出来子类 `Derived` 的话也能用，所以 `IEnumerable<Derived>` 的这个功能就覆盖了 `IEnumerable<Base>` 的功能。

`Action<>` 是逆变的，`Action<Base>` 可以当成 `Action<Derived>` 用。

`Func` 比较复杂，返回值的部分是协变的，参数的部分是逆变的。

如果泛型接口或委托的泛型参数被声明为协变或逆变，该泛型接口或委托则被称为“[[变体]]”。对泛型类型参数使用 `out` 就会将其声明为协变；使用 `in` 就会将其声明为逆变。要用返回值就是协变，要传参进去就是逆变，这两个关键字 `out` 和 `in` 跟这个是对应的

## Reference Links

- [协变和逆变 (C#) | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/)
- [泛型中的协变和逆变 - .NET | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/standard/generics/covariance-and-contravariance)

## See also

- [.NET C#基础（8）：变体 - 协变、逆变与不变 - HiroMuraki - 博客园](https://www.cnblogs.com/HiroMuraki/p/16355137.html)
- [C# - 协变、逆变 看完这篇就懂了 - Virgil-Zhou - 博客园](https://www.cnblogs.com/VVStudy/p/11404300.html)
- [泛型接口中的变体 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-generic-interfaces)
- [创建变体泛型接口 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/creating-variant-generic-interfaces)
- [在泛型集合的接口中使用变体 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-interfaces-for-generic-collections)
- [委托中的变体 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/variance-in-delegates)
- [使用委托中的变体 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-in-delegates)
- [对 Func 和 Action 泛型委托使用变体 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/concepts/covariance-contravariance/using-variance-for-func-and-action-generic-delegates)