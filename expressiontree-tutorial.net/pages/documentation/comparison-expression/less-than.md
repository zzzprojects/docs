# LessThan

You can build an expression tree which contains a "less than" numeric comparison using `Expression.LessThan` method. For example, you have the following code.

```csharp
bool value = 31 < 37;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression expr = Expression.LessThan(
    Expression.Constant(31),
    Expression.Constant(37)
);


Console.WriteLine(Expression.Lambda<Func<bool>>(expr).Compile()());
```

[Try it online](https://dotnetfiddle.net/ZfiLPY)