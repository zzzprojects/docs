# Convert

You can build an expression tree which contains a type conversion operation using `Expression.Convert` method. For example, you have the following code.

```csharp
double val = 10.3;		
Console.WriteLine((int)val);
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
Expression convertExpr = Expression.Convert(
    Expression.Constant(val),
    typeof(Int16)
);

Console.WriteLine(Expression.Lambda<Func<Int16>>(convertExpr).Compile()());
```

[Try it online](https://dotnetfiddle.net/OhjGsS)