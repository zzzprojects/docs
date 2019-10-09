# LessThanOrEqual

You can build an expression tree which contains a "less than" numeric comparison using `Expression.LessThanOrEqual` method. For example, you have the following code.

```csharp
bool value = 31 <= 31;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression expr = Expression.LessThanOrEqual(
    Expression.Constant(31),
    Expression.Constant(31)
);


Console.WriteLine(Expression.Lambda<Func<bool>>(expr).Compile()());
```

[Try it online](https://dotnetfiddle.net/QVkw3B)