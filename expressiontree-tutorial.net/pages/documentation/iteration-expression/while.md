# While

You can build an expression tree to perform a while loop by using `Expression.Loop` method. For example, you have the following code.

```csharp
var result = 1;
while (n > 1)
{
    result = result * n;
    n--;
}
Console.WriteLine(result);
```

Here is the code that is required to build the same functionality using expression tree. 

 - It is complicated because there isn't any API to build a while loop
 - You need to build a loop that contains a conditional test, and a target label to break out of the loop.

```csharp
var nArgument = Expression.Parameter(typeof(int), "n");
var result = Expression.Variable(typeof(int), "result");

// Creating a label that represents the return value
LabelTarget label = Expression.Label(typeof(int));

var initializeResult = Expression.Assign(result, Expression.Constant(1));

// This is the inner block that performs the multiplication,
// and decrements the value of 'n'
var ifTrueBlock = Expression.Block(
    Expression.Assign(result,
        Expression.Multiply(result, nArgument)),
    Expression.PostDecrementAssign(nArgument)
);

var ifThenElseExpr = Expression.IfThenElse(
    Expression.GreaterThan(nArgument, Expression.Constant(1)),
    ifTrueBlock,
    Expression.Break(label, result)
);

// Creating a method body.
BlockExpression body = Expression.Block(
    new[] { result },
    initializeResult,
    Expression.Loop(ifThenElseExpr, label)
);

// Compile and run an expression tree.
var factorial = Expression.Lambda<Func<int, int>>(body, nArgument).Compile();

Console.WriteLine(factorial(6));
```

[Try it online](https://dotnetfiddle.net/krNPrL)