# For

You can build an expression tree to perform a for loop by using `Expression.Loop` method. For example, you have the following code.

```csharp
int initValue = 0;
int maxValue = 5;
for (int loopVar = initValue; loopVar < maxValue; loopVar++)
{
    Console.WriteLine(loopVar);
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
public static Expression For(ParameterExpression loopVar, Expression initValue, Expression condition, Expression increment, Expression loopContent)
{
    var initAssign = Expression.Assign(loopVar, initValue);

    var breakLabel = Expression.Label("LoopBreak");

    var ifThenElseExpr = Expression.IfThenElse(
        condition,
        Expression.Block(
            loopContent,
            increment
        ),
        Expression.Break(breakLabel)
    );

    var loop = Expression.Loop(ifThenElseExpr, breakLabel);

    var block = Expression.Block(new[] { loopVar },
        initAssign,
        loop
    );

    return block;
}
```

This function can be easily used as shown below.

```csharp
int initValue = 0;
int maxValue = 5;

var loopVar = Expression.Parameter(typeof(int), "loopVar");
var loopBody = Expression.Call(typeof(Console).GetMethod("WriteLine", new[] { typeof(int) }), loopVar);

var initValueExpr = Expression.Constant(initValue);
var incrementExpr = Expression.PostIncrementAssign(loopVar);
var conditionExpr = Expression.LessThan(loopVar, Expression.Constant(maxValue));
var loop = For(loopVar, initValueExpr, conditionExpr, incrementExpr, loopBody);

var result = Expression.Lambda<Action<int>>(loop, loopVar).Compile();
result(initValue);
```

[Try it online](https://dotnetfiddle.net/dPHfT8)