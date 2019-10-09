# Division

You can build an expression tree which contains an arithmetic division operation using `Expression.Divide` method. For example, you have the following code.

```csharp
int value = 30 / 6;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression valueExpr = Expression.Divide (
    Expression.Constant(30),
    Expression.Constant(6)
);

Console.WriteLine(Expression.Lambda<Func<int>>(valueExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/aGGZro)