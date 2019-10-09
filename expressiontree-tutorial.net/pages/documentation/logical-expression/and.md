# And

You can build an expression tree which contains a bitwise **AND** operation using `Expression.And` method. For example, you have the following code.

```csharp
bool value = true & false;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.And(
    Expression.Constant(true),
    Expression.Constant(false)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/k5JbIq)