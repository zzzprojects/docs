# Overview

## Introduction

In .NET, an expression tree is a tree-like data structure, where each node is an expression, for example, a method call or a binary operation such as `x < y`.

 - You can compile and run the code represented by expression trees. 
 - It allows you to modify the executable code, the execution of LINQ queries in various databases, and the creation of dynamic queries dynamically.

## Create Expression Trees

C# or Visual Basic compiler create an expression tree for based on an anonymous lambda expression, but you can create expression trees manually by using the `System.Linq.Expressions` namespace.

### Using Lambda Expressions

When you assign a lambda expression to a variable of type `Expression<TDelegate>`, the compiler emits code to build an expression tree that represents the lambda expression.

 - The C# compiler generates expression trees only from single-line lambdas. 
 - It cannot parse statement multi-line lambdas. 

In this example, the C# compiler creates an expression tree that represents the lambda expression `val => val < 10`.

```csharp
Expression<Func<int, bool>> expression = val => val < 10;
var cResults = cExpression.Compile();
```

### Using the API

You can use the [Expression](https://docs.microsoft.com/en-us/dotnet/api/system.linq.expressions.expression?view=netframework-4.7.2) class which contains static factory methods that create expression tree nodes of specific types. The following example manually builds the expression tree for the lambda expression `val => val < 10`.

```csharp
ParameterExpression numParam = Expression.Parameter(typeof(int), "val");
ConstantExpression ten = Expression.Constant(10, typeof(int));
BinaryExpression numLessThanTen = Expression.LessThan(numParam, ten);

Expression<Func<int, bool>> expression =
    Expression.Lambda<Func<int, bool>>(
        numLessThanTen,
        new ParameterExpression[] { numParam });

var eResults = expression.Compile();
```

[Try it online](https://dotnetfiddle.net/shw64W)