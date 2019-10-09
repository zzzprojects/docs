---
PermaID: 1000005
Name: C# Eval Expression
---

# Eval Expression.NET

## Definition

The **Eval-Expression.NET** library allows evaluating, compiling and executing C# code and expression at runtime.

It supports nearly everything including:

 - Anonymous Type
 - Extension Methods
 - Generic Type
 - Lambda Expression
 - String Interpolation

## Eval.Execute

Execute a C# expression and return the result.


```csharp
int result = Eval.Execute<int>("X + Y", new { X = 1, Y = 2});

int result = Eval.Execute<int>(@"
    var list = new List<int>() { 1, 2, 3, 4, 5 };
    var filter = list.Where(x => x < 4);
    return filter.Sum(x => x);");
```

[Learn more](http://eval-expression.net/eval-execute)

## Eval.Compile

Compile a C# expression and return a delegate.


```csharp
// using Z.Expressions; // Don't forget to include this.

string code = "Price * Quantity";
var compiled = Eval.Compile<Func<OrderItem, decimal>>(code);

decimal totals = 0;
foreach(var order in orders)
{
    totals += compiled(order);
}
```

[Learn more](http://eval-expression.net/eval-compile)

## Eval Dynamic LINQ

The Eval-Expression.NET library makes it possible and very easy by allowing dynamic query with the same syntax as LINQ.

Everything you do in LINQ, you can do it dynamically with this library. The C# dynamic expression is not interpreted but compiled into LINQ expression.


```csharp
var list = new List<int>() { 1, 2, 3, 4, 5 };

var linqStatic  = list.Where(x => x > 2);
var linqDynamic = list.Where(x => "x > 2");
```

[Learn more](http://eval-expression.net/linq-dynamic)

## Requirements

### Entity Framework Version

 - All versions are supported.

[Learn more about the library](http://eval-expression.net)