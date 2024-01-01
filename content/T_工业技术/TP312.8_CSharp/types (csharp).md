---
aliases:
  - types
  - 类型
type: concepts
tags:
  - CSharp
  - types
---

C# 类可以自引用，因为 C# 中的类是[[reference type (csharp)|引用类型]]。

C# 中的

```csharp
class Entry
{
    public Entry next;
}
```

等于 C++中的

```cpp
class Entry
{
    public Entry* next;
}
```

（C++的类类型是[[value type (cpp)|值类型]]）

# C# 的类型系统

- [[value type (csharp)|值类型]]
	- [[value type (csharp)#内置值类型|简单类型]]
	- [有符号整型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/integral-numeric-types)：`sbyte`、`short`、`int`、`long`
	- [无符号整型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/integral-numeric-types)：`byte`、`ushort`、`uint`、`ulong`
	- [Unicode 字符](https://learn.microsoft.com/zh-cn/dotnet/standard/base-types/character-encoding-introduction)：`char`，表示 UTF-16 代码单元
	- [IEEE 二进制浮点](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types)：`float`、`double`
	- [高精度十进制浮点数](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/floating-point-numeric-types)：`decimal`
	- 布尔值：`bool`，表示布尔值（true 或 false）
- [ [[枚举]]类型]( https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/enum )
	- `enum E {...}` 格式的用户定义类型。 enum 类型是一种包含已命名常量的独特类型。 每个 enum 类型都有一个基础类型（必须是八种整型类型之一）。 enum 类型的值集与基础类型的值集相同。
- [[struct|结构类型]] ( https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/struct )
	- 格式为 `struct S {...}` 的用户定义类型
- [可以为 null 的值类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/nullable-value-types)
	- 值为 null 的其他所有值类型的扩展
- [元组值类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/value-tuples)
	- 格式为 (T1, T2, ...) 的用户定义类型
- [[reference type (csharp)|引用类型]]
- [类类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/class)
	- 其他所有类型的最终基类：`object`
	- [Unicode 字符串](https://learn.microsoft.com/zh-cn/dotnet/standard/base-types/character-encoding-introduction)：`string`，表示 UTF-16 代码单元序列
	- 格式为 `class C {...}` 的用户定义类型
- [接口类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/interface)
	- 格式为 `interface I {...}` 的用户定义类型
- [数组类型](https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/arrays/)
	- 一维、多维和交错。例如：int[]、int[,] 和 int[][]
- [[delegate (csharp)|委托]]类型
	- 格式为 `delegate int D(...)` 的用户定义类型

# Reference Links

- [https://learn.microsoft.com/zh-cn/dotnet/csharp/tour-of-csharp/](https://learn.microsoft.com/zh-cn/dotnet/csharp/tour-of-csharp/)

