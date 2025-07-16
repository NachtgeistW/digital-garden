---
aliases: [内联数组]
type: concepts
tags:
  - array
  - CSharp
---

内联数组是一种大小固定的 `struct` 类型数组。给 runtime 团队和库作者用的，适合需要性能的地方，比如高性能计算和嵌入式系统。

虽然以前就已经可以用 `stackalloc` storage 或者指针来操作内存块了，但要用它就必须给程序集启用不安全的代码。从 C# 12 开始，可以用内联数组安全地操作内存缓冲区了。使用内联数组还可以避免函数调用和创建堆栈帧的开销，从而提高应用程序的性能。

声明的方式：

```csharp
[System.Runtime.CompilerServices.InlineArray(10)] 
public struct Buffer 
{
	private int _element0; 
}
```

内联数组是具有以下特征的 `struct`：

- 它包含单个字段。
- 结构未指定显式布局。

此外，编译器还会验证 `System.Runtime.CompilerServices.InlineArrayAttribute` 属性：

- 必须大于零 (`> 0`)。
- 目标类型必须是结构。

内联数组的用法与其他数组没差：

```csharp
var buffer = new Buffer();
for (int i = 0; i < 10; i++)
{
    buffer[i] = i;
}

foreach (var i in buffer)
{
    Console.WriteLine(i);
}
```


在大多数情况下，可以像访问数组一样访问内联数组，以读取和写入值。此外，还可以使用 [[range operators (csharp)|范围]]和[[member access operators (CSharp)|索引]]运算符。

对单个字段的类型有最低限制。 它不能是指针类型，但可以是任何引用类型或任何值类型。 几乎可以将内联数组与任何 C# 数据结构一起使用。

Find out [more about inline arrays in this article](https://learn.microsoft.com/dotnet/csharp/whats-new/csharp-12#inline-arrays).
