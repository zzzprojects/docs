---
PermaID: 101005
Name: Parse Method
---

# Parse Method

The `Parse` method is used for parsing and creating expression tree fragments that are defined in `System.Linq.Dynamic.Core.Parser.ExpressionParser` class. It uses the TextParser to parse the string into the specified result type.. 

```csharp
ParameterExpression x = Expression.Parameter(typeof(int), "x");
ParameterExpression y = Expression.Parameter(typeof(int), "y");

var symbols = new[] { x, y };

Expression body = new ExpressionParser(symbols, "(x + y) * 2", symbols, new ParsingConfig()).Parse(typeof(int));

LambdaExpression e = Expression.Lambda(body, new ParameterExpression[] { x, y });
```

[Try it online](https://dotnetfiddle.net/c8wsu2)


