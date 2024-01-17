---
aliases:
  - ref readonly
type: concepts
tags:
  - CSharp/CSharp12
---

`ref readonly` 提供了以值或引用的方式传递参数的组合。传递给 `ref readonly` 参数的必须是变量。与 [[ref parameter modifier (csharp)|ref]] 和 [[out parameter modifier (csharp)|out]] 参数类似，参数不应为[[literal (csharp)|字面量]]或[[constants (csharp)|常量]]。字面量参数会生成警告，并且编译器会创建一个临时变量。与 [[in parameter modifier (csharp)|in]] 参数一样，`ref readonly` 参数无法修改。当一个方法不修改参数但需要这个参数的内存位置时，应该将其声明为 `ref readonly` 。

Find out [more about `ref readonly` parameters in this article](https://learn.microsoft.com/dotnet/csharp/whats-new/csharp-12#ref-readonly-parameters).

## Reference Links

- [C# 12 中的新增功能 - C# 指南 - C# | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/csharp/whats-new/csharp-12#ref-readonly-parameters)
- https://devblogs.microsoft.com/dotnet/announcing-csharp-12/#alias-any-type#ref-readonly-parameters

## See also

- [[各修饰符的传参类型]]