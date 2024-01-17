---
aliases: [Conditional]
type: concepts
tags:
- CSharp
---

`Conditional` [[特性]]使得方法执行依赖于[[预处理符号]]。 `Conditional` 属性是 [ConditionalAttribute](https://learn.microsoft.com/zh-cn/dotnet/api/system.diagnostics.conditionalattribute) 的别名，可以应用于[[方法]]或[[特性类]]。

比如说，`Conditional` 特性可以与 `DEBUG` 标识符一起使用，以启用调试生成（而非发布生成）中的跟踪和日志记录功能，如下例所示：

```csharp
[Conditional("DEBUG")]
static void DebugMethod()
{
}
```

当调用标记有条件的方法时，只有在指定的预处理符号存在时，编译器才会将其包括在调用中，否则将忽略掉。该条件方法必须是类或结构声明中的方法，而且必须具有 `void` 返回类型。

与将方法封闭在 `#if…#endif` 块内相比，`Conditional` *更简洁且较不容易出错*。

如果某个方法有多个 `Conditional` 特性，只要定义了其中任意一个预处理符号，编译器就会把方法包含进调用中。例如下例，只要存在 `A` 或 `B` 二者之一都会导致方法调用：

```csharp
[Conditional("A"), Conditional("B")]
static void DoIfAorB()
{
    // ...
}
```

# Reference Links

- [C# 编译器解释的属性：杂项 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/attributes/general#conditional-attribute)