# Addition

You can build an expression tree which contains an arithmetic addition operation using `Expression.Add` method. For example, you have the following code.

```csharp
int sum = 3 + 6;
Console.WriteLine(sum);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression sumExpr = Expression.Add(
    Expression.Constant(3),
    Expression.Constant(6)
);

Console.WriteLine(Expression.Lambda<Func<int>>(sumExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/0bDCOp)