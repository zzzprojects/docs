# Switch 

The `Expression.Switch` creates a `SwitchExpression` that represents a switch statement that has a default case. 

 - All SwitchCase objects in a SwitchExpression object must have the same type unless the SwitchExpression has the type void.
 - Each SwitchCase object has an implicit break statement, which means that there is no implicit fall through from one case label to another.
 - If switchValue does not match any of the cases, the default case represented by default body is run.

For example, you have the following code.

```csharp
int value = 4;

switch (value)
{
    case 1:
        Console.WriteLine("First Case");
        break;
    case 2:
        Console.WriteLine("Second Case");
        break;
    case 3:
        Console.WriteLine("Third Case");
        break;
    default:
        Console.WriteLine("Default Case");
        break;
}
```

Here is the code that is required to build the same functionality using expression tree.

```csharp
ConstantExpression switchValue = Expression.Constant(4);

var defaultCase = WriteLineExpression("Default Case");
var case1 = WriteLineExpression("First Case");
var case2 = WriteLineExpression("Second Case");
var case3 = WriteLineExpression("Third Case");

// This expression represents a switch statement 
// that has a default case.
SwitchExpression switchExpr =
    Expression.Switch(
        switchValue,
        defaultCase,
        new SwitchCase[]
        {
            Expression.SwitchCase(case1,Expression.Constant(1)),
            Expression.SwitchCase(case2,Expression.Constant(2)),
            Expression.SwitchCase(case3,Expression.Constant(3))
        }
    );

Expression.Lambda<Action>(switchExpr).Compile()();
```

[Try it online](https://dotnetfiddle.net/NOpWh7)