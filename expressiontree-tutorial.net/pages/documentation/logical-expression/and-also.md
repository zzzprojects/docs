# AndAlso

You can build an expression tree using `Expression.AndAlso` method which contains a conditional **AND** operation that evaluates the second operand only if the first operand evaluates to **true**. For example, you have the following code.

```csharp
bool value = true && false;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.AndAlso(
    Expression.Constant(true),
    Expression.Constant(false)
);

Console.WriteLine(Expression.Lambda<Func<bool>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/DIS2yv)