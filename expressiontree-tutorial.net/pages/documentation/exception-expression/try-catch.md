# TryCatch

You can build an expression tree which contains a try-catch by using `Expression.TryCatch` method. It creates a `TryExpression` representing a try block with any number of catch statements and neither a fault nor finally block. 

For example, you have the following code.

```csharp
try
{
    Console.WriteLine("Try block");
    throw new DivideByZeroException();
    Console.WriteLine("After throwing exception");
}
catch (DivideByZeroException)
{
    Console.WriteLine("Catch block");
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
var tryBlock = Expression.Block(
    WriteLineExpression("Try block"),
    Expression.Throw(Expression.Constant(new DivideByZeroException())),
    WriteLineExpression("After throwing exception")
);

var catchBlock = Expression.Catch(
    typeof(DivideByZeroException),
    WriteLineExpression("Catch block")
);

TryExpression tryCatchExpr = Expression.TryCatch(tryBlock, catchBlock);

Expression.Lambda<Action>(tryCatchExpr).Compile()();
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

[Try it online](https://dotnetfiddle.net/0tr94H)