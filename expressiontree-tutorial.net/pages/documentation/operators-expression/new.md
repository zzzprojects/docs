# New

You can build an expression tree which contains calling of the parameterless constructor of the specified type using `Expression.New` method. For example, you have the following code.

```csharp
List<string> list = new List<string>();
```

Here is the code that is required to build the same functionality using expression tree. 

```csharp
NewExpression newExpression =
    System.Linq.Expressions.Expression.New(typeof(List<string>));

Console.WriteLine(newExpression.ToString());
```

[Try it online](https://dotnetfiddle.net/mXn9LZ)