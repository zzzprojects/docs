# Conditional Expression

Conditional Expression represents an expression that has a conditional operator. 

## Condition

You can use the `Expression.Condition` factory method to create a `ConditionalExpression`. For example, you have the following code.

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
 
Expression conditionExpr = Expression.Condition(
    Expression.Constant(num > 10),
    Expression.Constant("num is greater than 10"),
    Expression.Constant("num is smaller or equal to 10")
    );

// The following statement first creates an expression tree,
// then compiles it and then executes it.       
Console.WriteLine(Expression.Lambda<Func<string>>(conditionExpr).Compile()());
```

The Condition method evaluates the first expression which is the test condition and if it is true, the second expression will be executed otherwise the third expression will be executed. 

[Try it online](https://dotnetfiddle.net/3Cjycl)