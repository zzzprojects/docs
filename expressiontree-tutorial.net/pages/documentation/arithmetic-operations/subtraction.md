# Subtraction

You can build an expression tree which contains an arithmetic subtraction operation using `Expression.Subtract` method. For example, you have the following code.

```csharp
int value = 33 - 6;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression valueExpr = Expression.Subtract(
    Expression.Constant(33),
    Expression.Constant(6)
);

Console.WriteLine(Expression.Lambda<Func<int>>(valueExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/FYhcQS)