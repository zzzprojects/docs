# Multiplication 

You can build an expression tree which contains an arithmetic multiplication operation using `Expression.Multiply` method. For example, you have the following code.

```csharp
int value = 30 * 5;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression valueExpr = Expression.Multiply(
    Expression.Constant(30),
    Expression.Constant(5)
);

Console.WriteLine(Expression.Lambda<Func<int>>(valueExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/WMaA8n)