# Goto

You can build an expression tree which contains a goto statement using `Expression.Goto` method. For example, you have the following code.

```csharp
Console.WriteLine("First Statement");
goto JumpTo;
Console.WriteLine("Second  Statement");
JumpTo:
Console.WriteLine("Third Statement and Second Statement is skiped");
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
// A label expression of the void type that is the target for the GotoExpression.
LabelTarget JumpTo = Expression.Label();

// This block contains a GotoExpression.
// It transfers execution to a label expression that is initialized with the same LabelTarget as the GotoExpression.
// The types of the GotoExpression, label expression, and LabelTarget must match.
BlockExpression blockExpr = Expression.Block(
        WriteLineExpression("First Statment"),
        Expression.Goto(JumpTo),
        WriteLineExpression("Second  Statment"),
        Expression.Label(JumpTo),
        WriteLineExpression("Third Statment and Second Statment is skiped")
    );

Expression.Lambda<Action>(blockExpr).Compile()();
```

This example shows how to create an expression that contains a goto statement. Here is the code for `WriteLineExpression` method

```csharp
public static Expression WriteLineExpression(string s)
{
    return Expression.Call(null,
       typeof(Console).GetMethod("WriteLine", new Type[] { typeof(String) }),
       Expression.Constant(s));
}
``` 

[Try it online](https://dotnetfiddle.net/HT34IS)