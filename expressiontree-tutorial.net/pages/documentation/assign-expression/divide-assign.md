# DivideAssign

You can build an expression tree which contains a division assignment operation using `Expression.DivideAssign` method. For example, you have the following code.

```csharp
int myInt;
myInt = 60;
myInt /= 5;
Console.WriteLine(myInt);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
ParameterExpression myIntVariableExpr = Expression.Variable(typeof(int), "myInt");

BlockExpression addAssignExpr = Expression.Block(
    new ParameterExpression[] { myIntVariableExpr },
    Expression.Assign(myIntVariableExpr, Expression.Constant(60)),
    Expression.DivideAssign(
        myIntVariableExpr,
        Expression.Constant(5)
    )
);

Console.WriteLine(Expression.Lambda<Func<int>>(addAssignExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/XXDB4I)