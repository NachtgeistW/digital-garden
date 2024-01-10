---
title: 
aliases: 
type: concepts
tags: 
draft: true
---

Func 是 C# 预先定义好的[[delegate (csharp)|委托]]之一。它封装了一个有 `TResult` 类型的返回值的方法。

Func 可以有 0~16 个参数。

```csharp
public delegate TResult Func<in T1, ... in T16, out TResult>(T1 arg1, ... T16 arg16);
```

（[[in (csharp generic modifier)|in]] 在这里表示[[contravariance (csharp)|逆变]]）

使用例：

```csharp
class Program
{
    static int Sum(int x, int y)
    {
        return x + y;
    }

    static void Main(string[] args)
    {
        Func<int,int, int> add = Sum;

        int result = add(10, 10);

        Console.WriteLine(result); 
    }
}
```

与其他 delegate 一样，Func 也可以和[[anonymous method (csharp)|匿名函数]]、[[lambda 表达式]]一起使用：

```csharp
Func<int> getRandomNumber = delegate()
							{
								Random rnd = new Random();
								return rnd.Next(1, 100);
							};


Func<int> getRandomNumber = () => new Random().Next(1, 100);
Func<int, int, int>  Sum  = (x, y) => x + y;
```

## Reference Links

- [Func\<TResult\> Delegate (System) | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/api/system.func-1?view=net-8.0)
- [Func delegate in C#](https://www.tutorialsteacher.com/csharp/csharp-func-delegate)

## See also

- [[Action Delegate (csharp)|Action委托]]