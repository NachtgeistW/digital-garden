An empty expression can be used where an expression is expected but no action is desired. For example, you can use an empty expression as the last expression in a block expression. In this case, the block expression's return value is void.

```c#
// Add the following directive to your file:
// using System.Linq.Expressions;

// This statement creates an empty expression.
DefaultExpression emptyExpr = Expression.Empty();

// The empty expression can be used where an expression is expected, but no action is desired.
// For example, you can use the empty expression as the last expression in the block expression.
// In this case the block expression's return value is void.
var emptyBlock = Expression.Block(emptyExpr);
```

You use `Expression.Empty()` to return `void`, _when building an expression tree by hand_. Expression body has nothing to do with expression trees.
The namespace name (`System.Linq.Expressions`) is not enough? Then consider [the description of the namespace]( https://msdn.microsoft.com/en-us/library/system.linq.expressions (v=vs.110).aspx): "The System.Linq.Expressions namespace contains classes, interfaces and enumerations that enable language-level code expressions to be represented as objects in the form of expression trees." That does not describe what you're doing at all. 
Yes, they are not the same as empty body, but that also applies to `Expression.Empty()`. If you look at decompiled code, you will see `public void Foo() { Expression.Empty(); }`
