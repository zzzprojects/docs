# Continue

You can build an expression tree which contains a continue statement using `Expression.Continue` method. For example, you have the following code.

```csharp
int cValue = 0;
while (true)
{
    cValue++;
    if (cValue < 5)
    {
        if (cValue == 3)
        {
            continue;
        }
        else
        {
            Console.WriteLine(cValue);
        }
    }
    else
    {
        break;
    }
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
LabelTarget breakLabel = Expression.Label();

// A label that is used by the Continue statement and the loop it refers to.
LabelTarget continueLabel = Expression.Label();

// This expression represents a Continue statement.
Expression continueExpr = Expression.Continue(continueLabel);

// A variable that triggers the exit from the loop.
ParameterExpression count = Expression.Parameter(typeof(int));

var ifTrueBlock = continueExpr;

var ifFalseBlock = Expression.Call(
    null,
    typeof(Console).GetMethod("WriteLine", new Type[] { typeof(int) }),
    count
    );

var nestedIfThenElseExpr = Expression.IfThenElse(
    Expression.Equal(count, Expression.Constant(3)),
    ifTrueBlock,
    ifFalseBlock
    );

var ifThenElseExpr = Expression.IfThenElse(
    Expression.LessThan(count, Expression.Constant(5)),
    nestedIfThenElseExpr,
    Expression.Break(breakLabel, count)
);

var block = Expression.Block(Expression.PostIncrementAssign(count), ifThenElseExpr);
// A loop statement.
Expression loopExpr = Expression.Loop(block,
    breakLabel,
    continueLabel
);

Expression.Lambda<Action<int>>(loopExpr, count).Compile()(0);
```

[Try it online](https://dotnetfiddle.net/2GoTUR)