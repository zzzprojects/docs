# Iteration Expression 

Iteration Expression or loop expression represents an infinite loop, and it can be exited with **break**. For example, you have the following code.

```csharp
var result = 1;
while (value > 1)
{
    result = result * value;
    value--;
}
Console.WriteLine(result);
```

Here is the code that is required to build the same functionality using expression tree. The following example demonstrates how to create a block expression that contains a LoopExpression object by using the Loop method.

```csharp
ParameterExpression value = Expression.Parameter(typeof(int), "value");

// Creating an expression to hold a local variable. 
ParameterExpression result = Expression.Parameter(typeof(int), "result");

// Creating a label to jump to from a loop.
LabelTarget label = Expression.Label(typeof(int));

ConditionalExpression ifThenElseExpr = Expression.IfThenElse(
    Expression.GreaterThan(value, Expression.Constant(1)),
    Expression.MultiplyAssign(result,
        Expression.PostDecrementAssign(value)),
    Expression.Break(label, result)
);

LoopExpression loop = Expression.Loop(ifThenElseExpr, label);

// Creating a method body.
BlockExpression block = Expression.Block(
    new[] { result },
    Expression.Assign(result, Expression.Constant(1)),
    loop
);

// Compile and run an expression tree.
var factorial = Expression.Lambda<Func<int, int>>(block, value).Compile();

Console.WriteLine(factorial(6));
```

You aren't limited in what you can build using Expression APIs. However, the more complicated expression tree that you want to build, the more difficult the code is to manage and to read.

[Try it online](https://dotnetfiddle.net/2K6kd1)