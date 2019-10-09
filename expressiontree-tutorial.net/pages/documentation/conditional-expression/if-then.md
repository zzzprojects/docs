# IfThen

The `Expression.IfThen` method creates a `ConditionalExpression` that represents a conditional block with an if statement. For example, you have the following code.

```csharp
int num = 60;

if(num > 10)
{
    Console.WriteLine("num is greater than 10");
}
```

Here is the code that is required to build the same functionality using expression tree.

```csharp
int num = 60;

var testCondition = Expression.Constant(num > 10);
var ifTrueBlock = WriteLineExpression("num is greater than 10");
var ifThenExpr = Expression.IfThen(testCondition, ifTrueBlock);

Expression.Lambda<Action>(ifThenExpr).Compile()();
```

This example shows how to create an expression that represents an if block. Here is the code for `WriteLineExpression` method

```csharp
public static Expression WriteLineExpression(string s)
{
    return Expression.Call(null,
       typeof(Console).GetMethod("WriteLine", new Type[] { typeof(String) }),
       Expression.Constant(s));
}
``` 

[Try it online](https://dotnetfiddle.net/bHe6tK)