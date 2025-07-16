---
title: primary constructors
aliases: 主构造函数
type: concepts
tags:
  - constructor
  - CSharp/CSharp12
---

> Trarizon 2023-11-29 18:10:48  
> 就是把 constructor 移到标识符后面了而已  
> 
> Trarizon 2023-11-29 18:11:00  
> 顺便有时候能省一些字段定义

## Introduction

C# 12 扩展了主构造函数。现在，它可以在所有 `class` 和 `struct` 上使用，而不再局限于 [[record (CSharp) |`record`]] 类型。主构造函数允许在声明时为其定义构造函数参数：

```csharp
public class BankAccount(string accountID, string owner)
{
    public string AccountID { get; } = accountID;
    public string Owner { get; } = owner;

    public override string ToString() => $"Account ID: {AccountID}, Owner: {Owner}";
}
```

主构造函数参数最常见的用途是：

- 作为 `base()` 构造函数的调用参数。
- 初始化成员[[fields (CSharp)|字段]]或[[properties (CSharp)|属性]]。
- 在实例成员中引用构造函数参数。
- 移除[[依赖注入]]中的样板。(To remove boilerplate in dependency injection.)（这句话写得不明不白的，tutorial 里写的是“指定依赖项注入的参数”。）

主构造函数参数可以视作整个类声明范围内的参数。

主构造函数可以添加到下列类型中：`class`、`struct`、`record class` 和  `record struct`。
- 当用于 `class` 和 `struct` 类型时，主构造函数参数位于整个 `class` 或 `struct` 定义的范围内。它们可以初始化属性或字段，可用作方法或局部函数中的变量，也可以传递给基本构造函数。
- 当用于 `record` 类型时，编译器会为每个主构造函数参数生成一个公共属性 (public property)。这些属性只是为 `record` 类型自动生成的众多成员之一。

### 主构造函数

主构造函数的参数位于声明类型的整个主体中。

（重复）主构造函数参数可以视作整个类声明范围内的参数。一定要记住主构造函数参数要视作*参数*，即使它们存在于整个类定义范围内它们也是*参数*。下列几条规则明确指出了它们的参数性质：

1. 不需要主构造函数参数时，可以不存储它们。
2. 主构造函数参数不是类的成员。 例如，名为 `param` 的主构造函数参数不能作为 `this.param` 访问。
3. 可以分配主构造函数参数。
4. 主构造函数参数不会变成属性，除非是 `record` 类型。

这些规则与任何方法的参数相同，包括其他构造函数声明。

主构造函数表明这些参数对于该类型的任何实例都是必需的。类的所有其他显式编写的构造函数都**必须**通过 `this(...)` 构造函数调用直接或间接调用主构造函数。该规则可确保在类型主体中的任意位置都能分配主构造函数参数[^cite1]，以及把主构造函数参数完全分配给了所有构造函数[^cite2]。

```csharp
// name isn't captured in Widget.
// width, height, and depth are captured as private fields
public class Widget(string name, int width, int height, int depth) : NamedItem(name)
{
    public Widget() : this("N/A", 1,1,1) {} // unnamed unit cube

    public int WidthInCM => width;
    public int HeightInCM => height;
    public int DepthInCM => depth;

    public int Volume => width * height * depth;
}
```

### 依赖关系注入

主构造函数的另一个常见用途是指定依赖项注入的参数。下面的代码创建了一个简单的控制器，使用时需要有一个服务接口：

```csharp
public interface IService
{
    Distance GetDistance();
}

public class ExampleController(IService service) : ControllerBase
{
    [HttpGet]
    public ActionResult<Distance> Get()
    {
        return service.GetDistance();
    }
}
```

主构造函数清楚地指明了类中所需的参数。使用主构造函数参数就像使用类中的任何其他变量一样。

## Possible Future Work

- 据 [Feedback on primary constructors · dotnet/csharplang · Discussion #7667 
\- (github. com)]( https://github.com/dotnet/csharplang/discussions/7667 ) 里的提议，他们在考虑允许主构造函数的参数使用 `readonly` 修饰符，以表明想创建公共属性 (public property)。

## Reference Links

- 进一步了解在 `record` 和非 `record` 类型中使用主构造函数：[Tutorial: Explore primary constructors](https://learn.microsoft.com/dotnet/csharp/whats-new/tutorials/primary-constructors)
-  [What's new in C# 12 - C# Guide - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/whats-new/csharp-12#primary-constructors)

[^cite1]: *[Declare and use primary constructors in classes and structs - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/whats-new/tutorials/primary-constructors).* (2023). Every other constructor for a class **must** call the primary constructor, directly or indirectly, through a `this()` constructor invocation. That rule ensures that primary constructor parameters are assigned anywhere in the body of the type.

[^cite2]: *[Instance constructors - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/programming-guide/classes-and-structs/instance-constructors#primary-constructors).* (2023). A primary constructor indicates that these parameters are necessary for any instance of the type. Any explicitly written constructor must use the `this(...)` initializer syntax to invoke the primary constructor. That ensures that the primary constructor parameters are definitely assigned by all constructors.
