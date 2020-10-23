---
Permalink: expression-language
Name: Expression Language
---

# Expression Language

The Dynamic LINQ expression language provides a simple and convenient way of writing expressions that can be parsed into LINQ expression trees.

- The language supports most of the constructs of expression trees, but it is not a complete query or programming language.
- It is designed to be familiar to C#, VB, and SQL users; it explains why some operators are present in multiple forms, such as `&&` and `and`.

## Constants

The expression language defines the following constants:

- The `true` and `false` denote the values of the type `bool`.
- The `null` denotes a null reference, and the `null` constant is of type `Object`, who is also implicitly convertible to any reference type.

## Types

### Primitive Types

The primitive types correspond to the similarly named types in the System namespace of the .NET Framework Base Class Library.

The expression language defines the following primitive types:

- Boolean
- Byte
- Char
- DateTime
- DateTimeOffset
- Decimal
- Double
- Guid
- Int16
- Int32
- Int64
- Object
- SByte
- Single
- String
- TimeSpan
- UInt16
- UInt32
- UInt64

### Accessible Types

The expression language also defines a set of accessible types consisting of the primitive types, and the following types from the `System` namespace.

- Math
- Convert

The accessible types are the only types that can be explicitly referenced in expressions, and method invocations in the expression language are restricted to methods declared in the accessible types.

### Nullable

The nullable form of a value type is referenced by writing a `?` after the type name. For example, `Int32?` denotes the nullable form of `Int32`.

### Integral Types

The non-nullable and nullable forms of the types `SByte`, `Byte`, `Int16`, `UInt16`, `Int32`, `UInt32`, `Int64`, and `UInt64` are collectively called the integral types.

### Real Types

The non-nullable and nullable forms of the types `Single`, `Double`, and `Decimal` are collectively called the real types.

The integral types and real types are collectively called the numeric types.

## Conversions

### Implicit Conversion

The following conversions are implicitly performed by the expression language:

- Convert the `null` literal to any reference type or nullable type.
- Convert an integer literal to an integral type or real type provided the number is within the range of that type.
- Convert a real literal to a real type provided the number is within the range of that type.
- Convert a string literal to an enum type provided the string literal contains the name of a member of that enum type.
- Convert a source type that is assignment compatible with the target type according to the Type.IsAssignableFrom method in .NET.
- Convert a non-nullable value type to the nullable form of that value type.
- Convert a numeric type to another numeric type with a greater range.

### Explicit Conversion

The expression language allows explicit conversions using the syntax type `(_expr_)`, where type is a type name optionally followed by `?` and `_expr_` is an expression. This syntax may be used to perform the following conversions:

- Between two types provided `Type.IsAssignableFrom` is true in one or both directions.
- Between two types provided one or both are interface types.
- Between the nullable and non-nullable forms of any value type.
- Between any two types belonging to the set consisting of `SByte`, `Byte`, `Int16`, `UInt16`, `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, `Double`, `Char`, any `enum` type, as well as the nullable forms of those types.

## Operators

The expression language supported the following operators in order of precedence from highest to lowest. Operators in the same category have equal precedence. 

In the below table, `x`, `y`, and `z` denote expressions, `T` denotes a type, and `m` denotes a member.

| Category | Expression | Description |
| :--- | :--- | :--- |
| Primary | `x.m` | Instance field or instance field or property access. Any public field or property can be accessed. |
| Primary | `x.m(...)` | Instance [method invocation](#calling-method-and-constructor). The method must be public and must be declared in an [accessible type](#accessible-types). |
| Primary | `x[...]` | Array or indexer access. Multi-dimensional arrays are not supported. |
| Primary | `T.m` | Static field or static property access. Any public field or property can be accessed. |
| Primary | `T.m(...)` | Static [method invocation](#calling-method-and-constructor). The method must be public and must be declared in an [accessible type](#accessible-types). |
| Primary | `T(...)` | [Explicit conversion](#conversions) or [constructor invocation](#calling-method-and-constructor). Note that `new` is not required in front of a constructor invocation. |
| Primary | `new(...)` | [Data object initializer](#_Data_Object_Initializer). This construct can be used to perform dynamic projections. |
| Primary | `it` | [Current instance](#_Current_Instance). In contexts where members of a current object are implicitly in scope, `it` is used to refer to the entire object itself. |
| Primary | `x(...)` | [Dynamic lambda invocation](#_Dynamic_Expression_Invocation). Used to reference another dynamic lambda expression. |
| Primary | `np(x)` | Null Propagating expression. Will expand `it.Rel.Id` to `it != null && it.Rel != null ? it.Rel.Id : null`. |
| Primary | `np(x, y)` | Null Propagating expression with default value specified. Will expand `it.Rel.Id` to `it != null && it.Rel != null ? it.Rel.Id : y`. |
| Primary | `iif(x, y, z)` | Conditional expression. Alternate syntax for `x ? y : z`. |
| Unary | `-x` | Negation. Supported types are `Int32`, `Int64`, `Decimal`, `Single`, and `Double`. |
| Unary | `!x` <br/> `not x` | Logical negation. Operand must be of type `Boolean`. |
| Multiplicative | `x * y` | Multiplication. Supported types are `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, and `Double`. |
| Multiplicative | `x / y` | Division. Supported types are `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, and `Double`. |
| Multiplicative | `x % y` <br/> `x mod y` | Remainder. Supported types are `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, and `Double`. |
| Additive | `x + y` | Addition or string concatenation. Performs string concatenation if either operand is of type `String`. Otherwise, performs addition for the supported types `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, `Double`, `DateTime`, and `TimeSpan`. |
| Additive | `x – y` | Subtraction. Supported types are `Int32`, `UInt32`, `Int64`, `UInt64`, `Decimal`, `Single`, `Double`, `DateTime`, and `TimeSpan`. |
| Additive | `x & y` | String concatenation. Operands may be of any type. |
| Relational | `x = y` <br/> `x == y` <br/> `x eq y` <br/> `x equal y` | Equal. Supported for reference types and the [primitive types](#primitive-types). Assignment is not supported. Comparison with null: `x == null` is supported, but `x is null` is not supported. |
| Relational | `x != y` <br/> `x <> y` <br/> `x ne y` <br/> `x neq y` <br/> `x notequal y`| Not equal. Supported for reference types and the [primitive types](#primitive-types). |
| Relational | `x < y` <br/> `x lt y` <br/> `x LessThan y` | Less than. Supported for all [primitive types](#primitive-types) except `Boolean`, `Object` and `Guid`. |
| Relational | `x > y` <br/> `x gt y` <br/> `x GreaterThan y` | Greater than. Supported for all [primitive types](#primitive-types) except `Boolean`, `Object` and `Guid`. |
| Relational | `x <= y` <br/> `x le y` <br/> `x LessThanEqual y` | Less than or equal. Supported for all [primitive types](#primitive-types) except `Boolean`, `Object` and `Guid`. |
| Relational | `x >= y` <br/> `x ge y` <br/> `x GreaterThanEqual y` | Greater than or equal. Supported for all [primitive types](#primitive-types) except `Boolean`, `Object` and `Guid`. |
| Logical AND | `x && y` <br/> `x and y`<br/> `x AndAlso y` | Logical AND. Operands must be of type `Boolean`. |
| Logical OR | `x || y` <br/> `x or y` <br/> `x OrElse y` | Logical OR. Operands must be of type `Boolean`. |
| Conditional | `x ? y : z` | Evaluates `y` if `x` is `true`, evaluates `z` if `x` is `false`. |
| Conditional | `x ?? y` | Evaluates `x` if `x` is not null else evaluates `y`. (Null Coalescing) |

### it- Example

```csharp
var baseQuery = new int[] { 1, 2, 3, 4, 5 }.AsQueryable();

var result1 = baseQuery.Select("it * $");

var result2 = baseQuery.Where("it % 2 = 0");
```

[Try it online](https://dotnetfiddle.net/3SlAvq)

### in- Example

```csharp
var rangeOfNumbers = Enumerable.Range(1, 100).ToArray();
var result1 = rangeOfNumbers.AsQueryable().Where("it in (1,3,5,7, 101)").ToArray();

var values = new int[] { 2, 4, 6, 8, 102};
var result2 = rangeOfNumbers.AsQueryable().Where("it in @0", values).ToArray();
```

[Try it online](https://dotnetfiddle.net/uhHUEO)

### Conditional Operator- Example

```csharp
var baseQuery = new int[] { 1, 2, 3, 4, 5 }.AsQueryable();

var result = baseQuery.Select("it % 2 == 0 ? true : false");
```

[Try it online](https://dotnetfiddle.net/uaGRMJ)

### iif- Example

```csharp
var baseQuery = new int[] { 1, 2, 3, 4, 5 }.AsQueryable();

var result = baseQuery.Select("iif(it % 2 = 0, true, false)");
```

[Try it online](https://dotnetfiddle.net/nCiBQQ)

## Calling Method and Constructor

The expression language can only call those methods and constructors that are declared public in the accessible types. This restriction protects the unintended side effects of calling arbitrary methods.

- The expression language permits only getting the value of any public field, property, or indexer.
- Overload resolution for methods, constructors, and indexers uses rules like C#.
- In informal terms, overload resolution will pick the best matching method, constructor, or indexer, or report an ambiguity error if no single best match can be identified.
- When you want to call a constructor, there is no need for the new keyword.

```csharp
using (var context = new EntityContext())
{
    var orders = context.Orders.Where("OrderDate >= DateTime(2020, 4, 1)").ToList();
}
```

[Try it online](https://dotnetfiddle.net/BWGuvY)

The example above creates a DateTime instance for a specific year, month, and day by calling a constructor.

## Data Object Initializers

A data object initializer creates a data class and returns an instance of that class. The properties of the data class are inferred from the data object's initializer.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers
        .Select("new (Name, CompanyName as Company, Phone)");
}
```

[Try it online](https://dotnetfiddle.net/5QHCHB)

The example above creates a data class with three properties, `Name`, `Company`, and `Phone`, and returns a sequence of instances of that data class initialized from the `Name`, `CompanyName`, and `Phone` properties of each customer.

## Current Instance

When parsing a lambda expression with a single unnamed parameter, the members of the unnamed parameter are automatically in scope in the expression string, and the current instance given by the unnamed parameter can be referenced in whole using the keyword `it`. 

For example, here is a simple example using a single unnamed parameter.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.Where("City = @0", "Paris").ToList();
}
```

The equivalent of the above example using the `it` keyword.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.Where("it.City = @0", "Paris").ToList();
}
```

[Try it online](https://dotnetfiddle.net/ihDmxs)

The IQueryable extension methods all parse their expression arguments as lambda expressions with a single unnamed parameter.

## Enum type

The expression language supports an implicit conversion from a string literal to an enum type provided the string literal contains the name of a member of that `enum type.

```csharp
using (var context = new EntityContext())
{
    var list = context.Customers.Where("OrderDate.DayOfWeek = @0", DayOfWeek.Monday); 
}
```

[Try it online](https://dotnetfiddle.net/UsJYc1)

## Sequence operators

A subset of the Standard Query Operators is supported for objects that implement IEnumerable&lt;T&gt; or IQueryable&lt;T&gt;. Specifically, the following constructs are permitted, where **seq** is an IEnumerable&lt;T&gt; or IQueryable&lt;T&gt; instance, **predicate** is a boolean expression and **selector** is an expression of any type.

- seq.All(predicate)
- seq.Any()
- seq.Any(predicate)
- seq.Average(selector)
- seq.Cast(string type)
- seq.Contains(selector)
- seq.ContainsKey(selector)
- seq.Count()
- seq.Count(predicate)
- seq.DefaultIfEmpty()
- seq.DefaultIfEmpty(defaultValue)
- seq.Distinct()
- seq.First()
- seq.First(predicate)
- seq.FirstOrDefault()
- seq.FirstOrDefault(predicate)
- seq.GroupBy(keySelector)
- seq.GroupBy(keySelector,  elementSelector)
- seq.Last()
- seq.Last(predicate)
- seq.LastOrDefault()
- seq.LastOrDefault(predicate)
- seq.LongCount()
- seq.LongCount(predicate)
- seq.Max(selector)
- seq.Min(selector)
- seq.OfType(string type)
- seq.OrderBy(selector)
- seq.OrderByDescending(selector)
- seq.Select(selector)
- seq.SelectMany(selector)
- seq.Single()
- seq.Single(predicate)
- seq.SingleOrDefault()
- seq.SingleOrDefault(predicate)
- seq.Skip(count)
- seq.SkipWhile(predicate)
- seq.Sum(selector)
- seq.Take(count)
- seq.TakeWhile(predicate)
- seq.ThenBy(selector)
- seq.ThenByDescending(selector)
- seq.Where(predicate)

In the predicate and selector expressions, the members of the current instance for that sequence operator are automatically in scope, and the instance itself can be referenced using the keyword **it**. For example:

``` csharp
customers.Where("Orders.Any(Total >= 1000)");
```
