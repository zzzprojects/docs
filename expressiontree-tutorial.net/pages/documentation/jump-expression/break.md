# Break

You can build an expression tree which contains a break statement using `Expression.Break` method. For example, you have the following code.

```csharp
int cValue = 5;    
int cResult;       
cResult = 1;       
while (true)       
{
    if (cValue > 1) 
    {              
        cResult = cResult * cValue--;
    }
    else
    {
        break;     
    }              
}

Console.WriteLine(cResult);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
// Creating a parameter expression.
ParameterExpression value = Expression.Parameter(typeof(int), "value");

// Creating an expression to hold a local variable. 
ParameterExpression result = Expression.Parameter(typeof(int), "result");

// Creating a label to jump to from a loop.
LabelTarget label = Expression.Label(typeof(int));

var ifThenElseExpr = Expression.IfThenElse(
    // Condition: value > 1
    Expression.GreaterThan(value, Expression.Constant(1)),
    // If true: result *= value --
    Expression.MultiplyAssign(result,
        Expression.PostDecrementAssign(value)),
    // If false, exit the loop and go to the label.
    Expression.Break(label, result)
);

// Creating a method body.
BlockExpression block = Expression.Block(
    // Adding a local variable. 
    new[] { result },
    // Assigning a constant to a local variable: result = 1
    Expression.Assign(result, Expression.Constant(1)),
    // Adding a loop.
    Expression.Loop(ifThenElseExpr, label)
);

// Compile and run an expression tree.
int factorial = Expression.Lambda<Func<int, int>>(block, value).Compile();

Console.WriteLine(factorial(5));
```

[Try it online](https://dotnetfiddle.net/nxTiMB)