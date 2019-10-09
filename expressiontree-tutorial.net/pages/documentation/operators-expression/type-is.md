# TypeIs

You can build an expression tree which checks if an object is compatible with a given type using `Expression.TypeIs` method. For example, you have the following code.

```csharp
Employee e = new Employee();
object objEmp = e as object;

if(objEmp is Employee)
{
    Console.WriteLine("It is an Empoyee object");
}
else
{
    Console.WriteLine("It is not an Empoyee object");
}
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
TypeBinaryExpression testCondition = Expression.TypeIs(
    Expression.Constant(objEmp),
    typeof(Employee));

var ifTrueBlock = WriteLineExpression("It is an Empoyee object");
var ifFalseBlock = WriteLineExpression("It is not an Empoyee object");
var ifThenExpr = Expression.IfThenElse(testCondition, ifTrueBlock, ifFalseBlock);

Expression.Lambda<Action>(ifThenExpr).Compile()();
```

[Try it online](https://dotnetfiddle.net/PQpVqQ)