---
PermaID: 101005
Name: Parse Method
---

# Parse Method

The `Parse` methods are also not yet supported in `System.Linq.Dynamic.Core` library, at the time of writing this document, it is only part of `System.Linq.Dynamic library`. The `Parse` method is used for parsing and creating expression tree fragments that are defined in `System.Linq.Dynamic.DynamicExpression`.

 - The `Parse` method parses the given expression and returns an expression tree. 
 - If the `resultType` parameter is non-null it specifies the required result type of the expression. 
 - The values parameter supplies zero or more substitution values that may be referenced in the expression.
 - The Parse method returns an "unbound" expression tree fragment, unlike the ParseLambda methods.

```csharp
ParameterExpression x = Expression.Parameter(typeof(int), "x");
ParameterExpression y = Expression.Parameter(typeof(int), "y");

Dictionary<string, ParameterExpression> symbols = new Dictionary<string, ParameterExpression>();

symbols.Add("x", x);
symbols.Add("y", y);

Expression body = System.Linq.Dynamic.DynamicExpression
    .Parse(null, "(x + y) * 2", symbols);

LambdaExpression e = Expression.Lambda(body, new ParameterExpression[] { x, y });
```

The above example uses `Parse` to produce the same result as a previous example.
