# Not

You can build an expression tree which contains a bitwise complement operation using `Expression.Not` method. For example, you have the following code.

```csharp
bool value = false;
Console.WriteLine(!value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.Not(
    Expression.Constant(false));

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/FfdWMk)