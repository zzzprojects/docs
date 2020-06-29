---
Permalink: advanced-parse-lambda
Name: ParseLambda
---

# DynamicExpressionParser

## ParseLambda Methods

The `System.Linq.Dynamic.Core.DynamicExpressionParser` class defines the static `ParseLambda` method for dynamically parsing and creating lambda expressions.

Internally, this static method uses the `ExpressionParser` class to parse the expression. For an example, see [Try it online](https://dotnetfiddle.net/c8wsu2).

There are various overloads of `ParseLambda` which can be used as per your requirement.

## Expression Parameter

The first `ParseLambda` overload parses a lambda expression with the given parameters and expression body, and it will return an expression instance representing the result.

```csharp
ParameterExpression x = Expression.Parameter(typeof(int), "x");
ParameterExpression y = Expression.Parameter(typeof(int), "y");
LambdaExpression e = DynamicExpressionParser.ParseLambda(new ParameterExpression[] { x, y }, null, "(x + y) * 2");

```

[Try it online](https://dotnetfiddle.net/22vgqJ)

The example above creates and assigns an `Expression<Func<int, int, int>>` instance to `e` representing the expression `(x + y) * 2`.

If the 2nd parameter resultType is non-null, it specifies the required result type for the expression.

```csharp
LambdaExpression e = DynamicExpressionParser
    .ParseLambda(new ParameterExpression[] { x, y }, typeof(double), "(x + y) * 2");
```

The parsing operation will include an implicit conversion to the given result type, in this case yielding an expression instance.

## Unnamed Parameter

The second ParseLambda overload parses a lambda expression with a single unnamed parameter of a specified argumentType. 

```csharp
using (var context = new EntityContext())
{
    LambdaExpression e = DynamicExpressionParser.ParseLambda(
        typeof(Customer), typeof(bool),
        "City = @0 and Orders.Count >= @1",
        "London", 10);
}
```

[Try it online](https://dotnetfiddle.net/y1i7V0)

The example above creates and assigns an `Expression` instance to `e`. The **City** and **Orders** are members of `Customer` that are automatically in scope.

## Dynamic Lambda

An expression can reference other dynamic lambda expressions through dynamic lambda invocations. A dynamic lambda invocation consists of a substitution variable identifier that references an instance of `DynamicExpressionParser`, followed by an argument list. 

The arguments supplied must be compatible with the parameter list of the given dynamic lambda expression.

```csharp
using (var context = new EntityContext())
{
    var e1 = DynamicExpressionParser.ParseLambda<Customer, bool>(new ParsingConfig(), true, "City = @0", "London");
    var e2 = DynamicExpressionParser.ParseLambda<Customer, bool>(new ParsingConfig(), true, "c => c.CompanyName != \"test\"");

    var customers = context.Customers.ToList().AsQueryable().Where("@0(it) and @1(it)", e1, e2);
}
```

[Try it online](https://dotnetfiddle.net/PF9Htk)

The example above parses two separate dynamic lambda expressions and then combines them in a predicate expression through dynamic lambda invocations.

You can also combine static and dynamic lambda expressions as shown below.

```csharp
Expression<Func<Customer, bool>> e1 = c => c.City == "Paris";
var e2 = DynamicExpressionParser.ParseLambda<Customer, bool>(new ParsingConfig(), true, "c => c.CompanyName != \"test\"");

var customers = context.Customers.ToList().AsQueryable().Where("@0(it) and @1(it)", e1, e2);
```

[Try it online](https://dotnetfiddle.net/gLBpU1)
