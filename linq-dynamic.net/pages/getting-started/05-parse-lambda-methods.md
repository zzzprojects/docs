---
PermaID: 101004
Name: ParseLambda Methods
---

# ParseLambda Methods

The `ParseLambda` methods are not yet supported in `System.Linq.Dynamic.Core library`, at the time of writing this document, it is only part of `System.Linq.Dynamic` library. The `System.Linq.Dynamic.DynamicExpression` class defines the `ParseLambda` methods for dynamically parsing and creating lambda expressions.

There are various overloads of `ParseLambda` which can be used as per your requirement.

## Expression Parameter

The first `ParseLambda` overload parses a lambda expression with the given parameters and expression body and it will return an expression instance representing the result. 

```csharp
ParameterExpression x = Expression.Parameter(typeof(int), "x");
ParameterExpression y = Expression.Parameter(typeof(int), "y");
LambdaExpression e = System.Linq.Dynamic.Core.DynamicExpressionParser
    .ParseLambda(new ParameterExpression[] { x, y }, null, "(x + y) * 2");
```

The above example creates and assigns an `Expression<Func<int, int, int>>` instance to e representing the expression `(x + y) * 2`.

If the 2nd parameter resultType is non-null it specifies the required result type for the expression. 

```csharp
LambdaExpression e = System.Linq.Dynamic.Core.DynamicExpressionParser
    .ParseLambda(new ParameterExpression[] { x, y }, typeof(double), "(x + y) * 2");
```

The parsing operation will include an implicit conversion to the given result type, in this case yielding an Expression instance.

## Unnamed Parameter

The second ParseLambda overload parses a lambda expression with a single unnamed parameter of a specified argumentType. 

```csharp
using (var context = new EntityContext())
{
    LambdaExpression e = System.Linq.Dynamic.Core.DynamicExpressionParser.ParseLambda(
        typeof(Customer), typeof(bool),
        "City = @0 and Orders.Count >= @1",
        "London", 10);
}
```

The above example creates and assigns an `Expression` instance to “e”. The **City** and **Orders** are members of `Customer` that are automatically in scope.

## Dynamic Lambda

An expression can reference other dynamic lambda expressions through dynamic lambda invocations. A dynamic lambda invocation consists of a substitution variable identifier that references an instance of `System.Linq.Expressions.LambdaExpression`, followed by an argument list. 

The arguments supplied must be compatible with the parameter list of the given dynamic lambda expression.

```csharp
using (var context = new EntityContext())
{
    Expression<Func<Customer, bool>> e1 = System.Linq.Dynamic.DynamicExpression
        .ParseLambda<Customer, bool>("City = \"Paris\"");
    Expression<Func<Customer, bool>> e2 = System.Linq.Dynamic.DynamicExpression
        .ParseLambda<Customer, bool>("Orders.Count >= 3");

    var customers = context.Customers.Where("@0(it) and @1(it)", e1, e2).ToList();
}
```

The above example parses two separate dynamic lambda expressions and then combines them in a predicate expression through dynamic lambda invocations.

You can also combine static and dynamic lambda expressions as shown below.

```csharp
using (var context = new EntityContext())
{
    Expression<Func<Customer, bool>> e1 = c => c.City == "Paris";
    Expression<Func<Customer, bool>> e2 = System.Linq.Dynamic.DynamicExpression
        .ParseLambda<Customer, bool>("Orders.Count >= 3");

    var customers = context.Customers.Where("@0(it) and @1(it)", e1, e2).ToList();
}
```
