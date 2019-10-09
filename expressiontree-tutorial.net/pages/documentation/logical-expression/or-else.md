# OrElse

You can build an expression tree using `Expression.OrElse` method which contains a conditional **OR** operation that evaluates the second operand only if the first operand evaluates to **false**. For example, you have the following code.

```csharp
bool value = false || true;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.OrElse(
    Expression.Constant(false),
    Expression.Constant(true)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/8Xr38p)