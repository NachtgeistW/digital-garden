---
title: Intermediate Language
aliases: IL, 中间语言, Common Intermediate Language, CLI, MSIL
type: concepts
tags: 
- dotnet
- CSharp
---

IL 是用高级 .NET 语言编写的代码编译生成的产物。一旦编译任意一种 .NET 的语言[^2]，都将获得由 IL 构成的二进制文件。IL 独立于运行时之上运行的任何一种语言[^1]。

## JIT

从高级代码生成 IL 后， CLR 接管并开始执行即时编译（Just-In-Time compiling，或 JIT），将 IL 编译成可以在 CPU 上实际运行的机器代码。这样就把 IL 运行起来了。通过这种方式，CLR 知道你的代码在做什么，可以有效地进行管理。

## Reference Links

- [What is managed code? - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/standard/managed-code)

## See also


[^1]: 原文：...the IL is independent from any specific language that runs on top of the runtime. Run on top of 应该就是“运行于……之上”的意思。例如 [CompSci 1 : Prelab 5](https://courses.cs.duke.edu/cps001/spring09/lab/plab05.html) 里有一句 Operating systems provide a software platform on top of which other programs, called application programs, can run. The application programs must be written to run on top of a particular operating system.
[^2]: 现在应该有 C#, F# 和 Visual Basic。