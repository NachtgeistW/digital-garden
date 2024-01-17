---
title: value type
aliases: 
- 值类型
type: concepts
tags: 
- CSharp 
---

和[[reference type (csharp)|引用类型]]一样是 C# 中的两个重要类型。值类型的变量包含了该类型的实例（直接存储值本身），而引用类型的变量包含的是该类型实例的引用。

默认情况下，在[[assignment-operator|赋值]]、为方法传参以及返回方法的值的时候，它们会复制变量值。在变量值是值变量的情况下，复制的是对应的类型实例。

例如下例，两次对 `p1.X` 和 `p2.Y` 的赋值操作分别且只影响了存储在变量中的值类型实例：

```csharp
using System;

public struct MutablePoint  //注意 struct （结构类型）是值类型
{
    public int X;
    public int Y;

    public MutablePoint(int x, int y) => (X, Y) = (x, y);

    public override string ToString() => $"({X}, {Y})";
}

public class Program
{
    public static void Main()
    {
        var p1 = new MutablePoint(1, 2);
        var p2 = p1;
        p2.Y = 200;
        Console.WriteLine($"{nameof(p1)} after {nameof(p2)} is modified: {p1}");
        Console.WriteLine($"{nameof(p2)}: {p2}");

        MutateAndDisplay(p2);
        Console.WriteLine($"{nameof(p2)} after passing to a method: {p2}");
    }

    private static void MutateAndDisplay(MutablePoint p)
    {
        p.X = 100;
        Console.WriteLine($"Point mutated in a method: {p}");
    }
}
// Expected output:
// p1 after p2 is modified: (1, 2)
// p2: (1, 200)
// Point mutated in a method: (100, 200)
// p2 after passing to a method: (1, 200)
```

如果值类型包含引用类型的数据成员，则在复制值类型实例时，只会复制对引用类型实例的引用。副本和原始值类型实例都具有对同一引用类型实例的访问权限。

例如下例，为 `List<string>` 的赋值操作同时影响了 `n1` 和 `n2`，而对 `Number` 的赋值操作只影响了 `n2`：

```csharp
using System;
using System.Collections.Generic;

public struct TaggedInteger
{
    public int Number;  //值类型
    private List<string> tags;  //引用类型

    public TaggedInteger(int n)
    {
        Number = n;
        tags = new List<string>();
    }

    public void AddTag(string tag) => tags.Add(tag);

    public override string ToString() => $"{Number} [{string.Join(", ", tags)}]";
}

public class Program
{
    public static void Main()
    {
        var n1 = new TaggedInteger(0);
        n1.AddTag("A");
        Console.WriteLine(n1);  // output: 0 [A]

        var n2 = n1;
        n2.Number = 7;
        n2.AddTag("B");

        Console.WriteLine(n1);  // output: 0 [A, B]
        Console.WriteLine(n2);  // output: 7 [A, B]
    }
}
```

*若要使代码更不易出错、更可靠，请定义并使用不可变的值类型。*

## 值类型的种类以及类型约束

值类型可以是以下种类之一：

- [[struct|结构类型]]，用于封装数据和相关功能
- [[enum|枚举类型]]，由一组命名常数定义，表示一个选择或选择组合

[[nullable value type|可空值类型]] `T?` 表示其基础值类型 `T` 的所有值以及一个额外的 [[null (csharp)]] 值。不能将 `null` 分配给值类型的变量[^note1]，除非它是可空值类型。

你可使用 [[struct 约束]] 将类型参数指定为可空值类型。结构类型和枚举类型都满足 `struct` 约束。

可在基类约束中使用 `System.Enum`（称为 [[enum 约束]]）将类型参数指定为枚举类型。

## 内置值类型

C# 提供了一些基本的值类型。它们也被叫做*简单类型*：
- [[整型数值类型]]
- [[floating-point-numeric-type (csharp)|浮点型数值类型]]
- [[bool (csharp)|bool]]，表示布尔值
- [[char (csharp)|char]]，表示 Unicode UTF-16 字符

所有简单值都是结构类型，而且它们与其他结构类型不同：它们允许特定的额外操作：

- 可以使用[[literal (csharp)|字面量]]为简单类型提供值。例如，`'A'` 是 `char` 类型的字面量，`2001` 是 `int` 类型的字面量。
    
- 可以使用 [[const (csharp)|const]] 关键字声明简单类型的常数。其他结构类型的常数不可能出现。
    
- [[expressions#constant-expression|常量表达式]] ——它们的操作数都是简单类型的常数——在编译时计算。
    
注意，[[value tuple (csharp)|值元组]]是值类型，而不是简单类型。

## Reference Links

- [Value types - C# reference - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/builtin-types/value-types)

## See also
- [[reference type (csharp)|引用类型]]


[^note1]: 值类型的变量永远不会为 `null`，因为值类型的值是其本身。