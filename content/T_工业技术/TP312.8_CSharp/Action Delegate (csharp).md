---
title: 
aliases: Action, Action委托
type: concepts
tags: 
draft: true
---

Action 是 C# 预先定义好的[[delegate (csharp)|委托]]之一。它封装了一个没有返回值的方法。

Action 可以有 0~16 个参数。

```csharp
public delegate void Action<in T1, ... in T16>(T1 arg1, ... T16 arg16);
```

（[[in (csharp generic modifier)|in]] 在这里表示[[contravariance (csharp)|逆变]]）

所以

```csharp
public delegate void Print(int val);

static void ConsolePrint(int i)
{
    Console.WriteLine(i);
}

static void Main(string[] args)
{           
    Print prnt = ConsolePrint;
    prnt(10);
}

```

可以写成

```csharp
static void ConsolePrint(int i)
{
    Console.WriteLine(i);
}

static void Main(string[] args)
{
    Action<int> printActionDel = ConsolePrint;
    printActionDel(10);
}
```

与其他 delegate 一样，Action 也可以和[[anonymous method (csharp)|匿名函数]]、[[lambda 表达式]]一起使用：

```csharp
static void Main(string[] args)
{
    Action<int> printActionDel2 = delegate(int i)
                                {
                                    Console.WriteLine(i);
                                };
    printActionDel2(10);

	Action<int> printActionDel3 = i => Console.WriteLine(i);
    printActionDel3(10);
}
/* Output: 
	10
	10
*/
```

## Reference Links

- [Action Delegate (System) | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/api/system.action?view=net-8.0)
- [Func and Action in C#: A Complete Guide | by Interviewer Live | Medium](https://medium.com/@interviewer.live/func-and-action-in-c-a-complete-guide-dfe8cf31581c)

## See also

- 