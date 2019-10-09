# Equal

You can build an expression tree which contains an equality comparison using `Expression.Equal` method. For example, you have the following code.

```csharp
bool value = 31 == 31;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.Equal(
    Expression.Constant(31),
    Expression.Constant(31)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/k3fxRS)