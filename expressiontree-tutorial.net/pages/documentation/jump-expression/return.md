# Return

You can build an expression tree which contains a return statement(s) using `Expression.Return` method. For example, you have the following code.

```csharp
Func<int, bool> myFunc = (x) => 
{
    if (x == 5)
        return true;
    else
        return false;
};
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
LabelTarget returnTarget = Expression.Label(typeof(bool));
ParameterExpression para = Expression.Parameter(typeof(int), "intvalue");
Expression test = Expression.Equal(para, Expression.Constant(5));
Expression iftrue = Expression.Return(returnTarget, Expression.Constant(true));
Expression iffalse = Expression.Return(returnTarget, Expression.Constant(false));

var ex = Expression.Block(
    Expression.IfThenElse(test, iftrue, iffalse),
    Expression.Label(returnTarget, Expression.Constant(false)));

var compiled = Expression.Lambda<Func<int, bool>>(
    ex,
    new ParameterExpression[] { para }
).Compile();

Console.WriteLine(compiled(5));     // prints "True"
```

This example shows how to create an expression that contains a return statement(s). 

[Try it online](https://dotnetfiddle.net/TfgSbX)