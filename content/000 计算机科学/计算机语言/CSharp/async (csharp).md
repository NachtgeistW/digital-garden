---
title: 
aliases: async
type: concepts
tags: 
- CSharp 
---

async 关键字是 C# 异步编程中的一个重要修饰符，通常与 [[await (csharp)|await]] 搭配使用。使用它将方法、[[lambda 表达式]]和[[匿名方法]]标记为可异步的，此时它们将变为异步方法。示例：

```csharp
public async Task<int> ExampleMethodAsync()
{
    //...
}
```

## 异步方法 {#async-method}

异步方法有两个功能：

- 异步方法会以同步的方式运行，直到遇到 `await` 表达式。方法会停在这里（挂起），直到被 await 的任务完成。完成后才能继续通过该点。挂起时，控制返回至异步方法的调用方。异步方法在 `await` 表达式执行时暂停并不构成方法退出，只会导致 finally 代码块不运行。

- 异步方法通常包含 `await` 运算符的一个或多个实例，不过缺少 `await` 表达式也不会报错，此时编辑器会发出警告。如果异步方法未使用 `await` 运算符标记暂停点，则该方法将同步执行。

`async` 关键字是上下文关键字，原因在于只有当它修饰方法、lambda 表达式或匿名方法时，它才是关键字。在所有其他上下文中，都会将其解释为标识符。

## Reference Links

- [async - C# Reference - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/language-reference/keywords/async)
- [The Task Asynchronous Programming (TAP) model with async and await" - C# | Microsoft Learn](https://learn.microsoft.com/en-gb/dotnet/csharp/asynchronous-programming/task-asynchronous-programming-model#BKMK_AsyncandAwait)

## See also

- [[asynchrounous programming with async and await|使用 async 和 await 的异步编程]]