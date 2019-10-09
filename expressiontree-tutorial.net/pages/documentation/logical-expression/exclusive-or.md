# ExclusiveOr

You can build an expression tree which contains a bitwise **XOR** operation using `Expression.ExclusiveOr` method. For example, you have the following code.

```csharp
bool value = true ^ false;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression expr = Expression.ExclusiveOr(
    Expression.Constant(true),
    Expression.Constant(false)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(expr).Compile()());
```

[Try it online](https://dotnetfiddle.net/GKSkuj)