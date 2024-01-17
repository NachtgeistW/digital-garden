---
title: reference type
aliases:
  - 引用类型
type: concepts
tags:
- CSharp
---

和[[value type (csharp)|值类型]]一样是 C# 中的两个重要类型。引用类型的变量存储的是对数据（对象）的引用，而值类型的变量直接存储数据本身。

使用引用类型即可让两个变量引用同一个对象，因此对一个变量的操作也会影响到另一个同样引用它的变量。而使用值类型时每个变量存储的都是数据的副本，所以对一个变量的操作不可能影响到另一个变量（除非是 `in`、`ref` 和 `out` 参数变量，参阅 [[in parameter modifier (csharp)|in]]、[[ref parameter modifier (csharp)|ref]] 和 [[out parameter modifier (csharp)|out]] 参数修饰符）。

下列关键字用于声明引用类型：

- [[class (csharp)|class]]
- [[interface (csharp)|interface]]
- [[delegate type (csharp)|delegate]]
- [[record (CSharp)|record]]

C# 也提供了下列内置引用类型：

- [[dynamic]]
- [[object (csharp)|object]]
- [[string (csharp)|string]]

## Reference Links

- 

## See also

- 