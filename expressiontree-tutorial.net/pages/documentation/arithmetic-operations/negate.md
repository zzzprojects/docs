# Negate

You can build an expression tree which contains an arithmetic negation operation using `Expression.Negate` method. For example, you have the following code.

```csharp
var value = 5;
value = -value;
Console.WriteLine(value);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression greaterThanExpr = Expression.Negate(
    Expression.Constant(5));

Console.WriteLine(Expression.Lambda<Func<int>>(greaterThanExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/Lan1HQ)