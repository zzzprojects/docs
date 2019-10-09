# TryFinally

You can build an expression tree which contains a try-finally blocks by using `Expression.TryFinally` method. It creates a `TryExpression` representing a try block with a finally block and no catch statements. 

For example, you have the following code.

```csharp
try
{
    Console.WriteLine("Try block");
}
finally
{
    Console.WriteLine("Finally block");
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
var tryBlock = WriteLineExpression("Try block");

var finallyBlock = WriteLineExpression("Finally block");

TryExpression tryFinallyExpr = Expression.TryFinally(tryBlock, finallyBlock);

Expression.Lambda<Action>(tryFinallyExpr).Compile()();
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

[Try it online](https://dotnetfiddle.net/uUn2NJ)