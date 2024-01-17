---
title: 
aliases: 
type: concepts
tags: 
- CSharp
draft: true
---

Extension methods enable you to "add" methods to existing types without creating a new derived type, recompiling, or otherwise modifying the original type. Extension methods are static methods, but they're called as if they were instance methods on the extended type. For client code written in C#, F# and Visual Basic, there's no apparent difference between calling an extension method and the methods defined in a type.

The most common extension methods are the LINQ standard query operators that add query functionality to the existing [System.Collections.IEnumerable](https://learn.microsoft.com/en-us/dotnet/api/system.collections.ienumerable) and [System.Collections.Generic.IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1) types. To use the standard query operators, first bring them into scope with a `using System.Linq` directive. Then any type that implements [IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1) appears to have instance methods such as [GroupBy](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.groupby), [OrderBy](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.orderby), [Average](https://learn.microsoft.com/en-us/dotnet/api/system.linq.enumerable.average), and so on. You can see these additional methods in IntelliSense statement completion when you type "dot" after an instance of an [IEnumerable<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.ienumerable-1) type such as [List<T>](https://learn.microsoft.com/en-us/dotnet/api/system.collections.generic.list-1) or [Array](https://learn.microsoft.com/en-us/dotnet/api/system.array).

## Reference Links

- 

## See also

- 