# Assign

You can build an expression tree which contains an assignment operation using `Expression.Assign` method. For example, you have the following code.

```csharp
string myString;
myString = "Hello, World!";   
Console.WriteLine(myString);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
ParameterExpression myStringVariableExpr = Expression.Variable(typeof(Stri

Expression assignExpr = Expression.Assign(
    myStringVariableExpr,
    Expression.Constant("Hello, World!")
    );

Expression blockExpr = Expression.Block(
    new ParameterExpression[] { myStringVariableExpr },
    assignExpr
    );

Console.WriteLine(Expression.Lambda<Func<String>>(blockExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/i7nmpN)