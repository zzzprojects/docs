---
Permalink:basic-simple-query
Name:Simple Query
---

# Simple Query

## Strongly typed LINQ versus Dynamic Linq

When querying data using Dynamic LINQ, everything can be expressed using strings. This includes predicates, selectors, and sorting.

Strongly typed LINQ looks like:

```csharp
1: var result = context.Customers
2:     .Where(c => c.City == "Paris")
3:     .ToList();
```

Dynamic LINQ looks like:

```csharp
1: var resultDynamic = context.Customers
2:     .Where("City == \"Paris\"")
3:     .ToList();
```

When we break down these examples:

1. The context.Customers in this case is a `DbSet<Customer>`, which implements `IQueryable<T>`. This is the same starting point for both examples.

2. The `Where`-predicate for Dynamic Linq is written as a string, where the same property is used (City) and the same value (Paris).
   
   Since the value "Paris" is a string itself, we need to escape the double quotes with a `\`. So the `"` becomes a `\"`.

   This also is required for the escape character `\` itself, so if you want to query on a string like `"\r\n"`, you need to double escape the escape characters, so it becomes:
   ```csharp
   .Where("Text == \"\\r\\n\"")
   ```
   
   For more examples, see this [unit-test](https://github.com/zzzprojects/System.Linq.Dynamic.Core/blob/master/test/System.Linq.Dynamic.Core.Tests/Parser/StringParserTests.cs#L92).

3. The `ToList()` method for both examples will just return a strongly typed list: `List<Customer>`.

Note that instead of using the query value directly in the string like `"City == \"Paris\""` you can also use [composite string formatting](https://docs.microsoft.com/en-us/dotnet/standard/base-types/composite-formatting). But instead of using the format item syntax `{n}`, you need to use `@n` (Where `n` is the index). So line 2 in the example above will become:

```csharp
.Where("City == @0", "Paris")`
```

And of course, it's not required to specify the value as a hard-coded value. So another valid Dynamic LINQ query example will be:

```csharp
1: string cityToSearch = "Paris";
2: var resultDynamic = context.Customers
3:     .Where("City == @0", cityToSearch)
4:     .ToList();
```

[View all above examples here](https://dotnetfiddle.net/cs6MRX)

See chapters below for more basic query examples.

## More `Where` examples

Note that it's also possible to query data using multiple predicates:

```csharp
1: var result = context.Customers
2:     .Where(c => c.City == "Paris" && c.Age > 50)
3:     .ToList();
```

This code can be used when using Dynamic LINQ:

```csharp
1: var resultDynamic = context.Customers
2:     .Where("City == \"Paris\" && Age > 50")
3:     .ToList();
```

Note that instead of the `&&`-operand, you can use `and`. Also in this example you can use composite string formatting. In that case, the Dynamic LINQ will be:

```csharp
1: var resultDynamic = context.Customers
2:     .Where("City == @0 and Age > @1", "Paris", 50)
3:     .ToList();
```

You can also mimic the strongly typed `Where`-predicate using the [=> operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/lambda-operator), like this:

```csharp
1: var resultDynamic = context.Customers
2:     .Where("c => c.City == \"Paris\" && c.Age > 50")
3:     .ToList();
```

[View all above examples here](https://dotnetfiddle.net/4yOUhM)

## Selecting entities and properties

Selecting entities or only the properties you need is another powerful feature of LINQ. Since the generated SQL will only return a limited set and not the whole entity, it is more useful if you only want to return a few columns.

Here is an example of a strongly typed:

```csharp
1: var result = context.Customers
2:     .Select(c => new { c.City, c.CompanyName } )
3:     .ToList();
```

Dynamic LINQ looks like:

```csharp
1: var resultDynamic = context.Customers
2:     .Select("new { City, CompanyName }")
3:     .ToDynamicList();
```

When we break down these two examples:

1. The context.Customers in this case is a `DbSet<Customer>`, which implements `IQueryable<T>`. This is the same starting point for both examples.
2. The `Select`-method for Dynamic Linq is written as a string, where the same properties are used (City and CompanyName).
3. For Dynamic LINQ we need to use the `ToDynamicList()` method, because the return from the `Select` is an `IQueryable`. The result from both the strongly typed and dynamic example is the same: a list from a dynamic-generated class with two properties (City and CompanyName).

[View all above examples here](https://dotnetfiddle.net/ZN3FSo)

## Ordering results

Sorting the result based on a property or multiple properties is another feature from LINQ. Observe the differences.

A strongly typed example to sort on City and then on CompanyName would look like:

```csharp
1: var ordered = context.Customers
2:     .OrderBy(c => c.City).ThenBy(c => c.CompanyName)
3:     .ToList();
```

Dynamic LINQ looks like:

```csharp
1: var orderedDynamic = context.Customers
2:     .OrderBy("City, CompanyName")
3:     .ToList();
```

When we break down these two examples:

1. The context.Customers in this case is a `DbSet<Customer>`, which implements `IQueryable<T>`. This is the same starting point for both examples.
2. In the strongly typed LINQ example, you need to call the `OrderBy`-method, followed by the `ThenBy`-method to sort the result; first on the City and then on the CompanyName.
For Dynamic Linq you only need to use the `OrderBy`-method, and provide the two sort properties as a comma separated string.
3. The `ToList()` method for both examples will return a strongly typed list: `List<Customer>`.

## Ordering results (combining ascending and descending)

In case you want to sort Descending, or a mixed combination, you can use the following Dynamic LINQ:

```csharp
1: var orderedDynamic = context.Customers
2:     .OrderBy("City").ThenBy("CompanyName desc")
3:     .ToList();
```

Or

```csharp
1: var orderedDynamic = context.Customers
2:     .OrderBy("City, CompanyName desc")
3:     .ToList();
```

The examples above will perform the sorting on the City property *Ascending* (= default) and then the sorting will be on the CompanyName in a *Descending* way.

[View all above examples here](https://dotnetfiddle.net/GdxsMG)