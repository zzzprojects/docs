# NotEqual

You can build an expression tree which contains an inequality comparison using `Expression.NotEqual` method. For example, you have the following code.

```csharp
bool value = 31 != 32;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.NotEqual(
    Expression.Constant(31),
    Expression.Constant(32)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/KvBH3t)