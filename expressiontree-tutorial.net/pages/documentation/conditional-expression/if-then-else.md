# IfThenElse

The `Expression.IfThenElse` method creates a `ConditionalExpression` that represents a conditional block with if and else statements. For example, you have the following code.

```csharp
int num = 60;

if(num > 10)
{
    Console.WriteLine("num is greater than 10");
}
else
{
    Console.WriteLine("num is less than or equan to 10");
}
```

Here is the code that is required to build the same functionality using expression tree.

```csharp
int num = 60;

var testCondition = Expression.Constant(num > 10);
var ifTrueBlock = WriteLineExpression("num is greater than 10");
var ifFalseBlock = WriteLineExpression("num is less than or equal to 10");

var ifThenElseExpr = Expression.IfThenElse(
    testCondition,
    ifTrueBlock,
    ifFalseBlock);

Expression.Lambda<Action>(ifThenElseExpr).Compile()();
```

This example shows how to create an expression that represents an if-else block. Here is the code for `WriteLineExpression` method

```csharp
public static Expression WriteLineExpression(string s)
{
    return Expression.Call(null,
       typeof(Console).GetMethod("WriteLine", new Type[] { typeof(String) }),
       Expression.Constant(s));
}
``` 

[Try it online](https://dotnetfiddle.net/v7jNni)
