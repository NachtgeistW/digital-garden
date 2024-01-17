---
aliases: [集合表达式]
type: concepts
tags: 
- CSharp/CSharp12
---

C# 12 以前，根据不同的场景，创建不同的[[集合]]需要不同的语法。初始化  `List<int>` 的语法又与 `int[]` 或 `Span<int>` 不同。

```csharp
int[] x1 = new int[] { 1, 2, 3, 4 };
int[] x2 = Array.Empty<int>();
WriteByteArray(new[] { (byte)1, (byte)2, (byte)3 });
List<int> x4 = new() { 1, 2, 3, 4 };
Span<DateTime> dates = stackalloc DateTime[] { GetDate(0), GetDate(1) };
WriteByteSpan(stackalloc[] { (byte)1, (byte)2, (byte)3 });
```

现在可以使用一种通用又简洁 (terse) 的语法——集合表达式——来创建与初始化它们了。

```csharp
int[] x1 = [1, 2, 3, 4];
int[] x2 = [];
WriteByteArray([1, 2, 3]);
List<int> x4 = [1, 2, 3, 4];
Span<DateTime> dates = [GetDate(0), GetDate(1)];
WriteByteSpan([1, 2, 3]);
```

在集合表达式里还可以使用[[分布元素]]，将一或多个集合或[[可枚举表达式]]的元素组合到一起：

```csharp
int[] numbers1 = [1, 2, 3];
int[] numbers2 = [4, 5, 6];
int[] moreNumbers = [.. numbers1, .. numbers2, 7, 8, 9];
// moreNumbers contains [1, 2, 3, 4, 5, 6, 7, 8, ,9];
```

## Possible Future Work

- 未来或许也会支持 [[字典]] 以及对 `var` 自动推算的支持。

## Reference Links

- https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/operators/collection-expressions
-  [What's new in C# 12 - C# Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/whats-new/csharp-12#collection-expressions)
