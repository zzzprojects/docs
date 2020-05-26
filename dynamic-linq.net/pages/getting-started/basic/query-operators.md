---
Permalink:basic-query-operators
Name:Query Operators
---
# Query Operators

## Standard Query Operators

The following table lists of all Dynamic Query Operators are supported on a `IQueryable` or `IQueryable<T>`.

Query Operator | Return Type | Info
:--| :- | :-
[Aggregate](#Aggregate) | dynamic | Dynamically runs an aggregate function.
[All](#All) | bool | Determines whether all the elements of a sequence satisfy a condition.
[Any](#Any) | bool | Determines whether a sequence contains any elements.
[Average](#Average) | Single numeric value | Computes the average of a sequence of numeric values.
[AsEnumerable](#AsEnumerable) | IQueryable&lt;dynamic&gt; | Returns the input typed as IEnumerable&lt;dynamic&gt;.
[Cast](#Is,As,Cast,OfType) | IQueryable | Converts the elements of the specified type.
[Count](#Count) | int | Returns the number of elements in a sequence.
[DefaultIfEmpty](#DefaultIfEmpty) | IQueryable | Returns the elements of the specified sequence or the type parameter's default value in a singleton collection if the sequence is empty.
[Distinct](#Distinct) | IQueryable | Returns distinct elements from a sequence by using the default equality comparer to compare values.
[First](#First-/-FirstOrDefault) | dynamic | Returns the first element of a sequence.
[FirstOrDefault](#First-/-FirstOrDefault) | dynamic | Returns the first element of a sequence, or a default value if the sequence contains no elements.
[GroupBy](#GroupBy) | IQueryable | Groups the elements of a sequence according to a specified key string function and creates a result value from each group and its key.
[GroupByMany](#GroupByMany) | IEnumerable&lt;GroupResult&gt; | Groups the elements of a sequence according to multiple specified key string functions and creates a result value from each group (and subgroups) and its key.
[GroupJoin](#GroupJoin) | IQueryable | Correlates the elements of two sequences based on equality of keys and groups the results. The default equality comparer is used to compare keys.
[Join](#join]) | IQueryable or IQueryable&lt;T&gt; | Correlates the elements of two sequences based on matching keys. The default equality comparer is used to compare keys.
[Last](#Last-/-LastOrDefault) | dynamic | Returns the last element of a sequence. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
[LastOrDefault](#Last-/-LastOrDefault) | dynamic | Returns the last element of a sequence, or a default value if the sequence contains no elements. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
[LongCount](#Count) | long | Returns the number of elements in a sequence as a long.
[OfType](#Is,As,Cast,OfType) | IQueryable | Filters the elements based on a specified type.
[OrderBy](#simple-query.md#ordering-results) | IOrderedQueryable or IOrderedQueryable&lt;T&gt; | Sorts the elements of a sequence in ascending or descending order according to a key.
[Page](#Page-/-PageResult) | IQueryable or IQueryable&lt;T&gt; | Returns the elements as paged.
[PageResult](#Page-/-PageResult) | PagedResult or PagedResult&lt;T&gt; | Returns the elements as paged and include the CurrentPage, PageCount, PageSize and RowCount.
[Reverse](#Reverse) | IQueryable | Inverts the order of the elements in a sequence. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
[Select](simple-query.md#strongly-typed-linq-versus-dynamic-linq) | IQueryable or IQueryable&lt;T&gt; | Projects each element of a sequence into a new form.
[SelectMany](#SelectMany) | IQueryable or IQueryable&lt;T&gt; | Projects each element of a sequence and combines the resulting sequences into one sequence.
[Single](#Single) | dynamic | Returns the only element of a sequence, and throws an exception if there is not exactly one element in the sequence.
[SingleOrDefault](#Single) | dynamic | Returns the only element of a sequence, or a default value if the sequence is empty; this method throws an exception if there is more than one element in the sequence.
[Skip](#Skip-/-SkipWhile) | IQueryable | Bypasses a specified number of elements in a sequence and then returns the remaining elements.
[SkipWhile](#Skip-/-SkipWhile) | IQueryable | Bypasses elements in a sequence as long as a specified condition is true and then returns the remaining elements. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
[Sum](#sum) | Single numeric value | Computes the sum of a sequence of numeric values.
[Take](#Take-/-TakeWhile) | IQueryable | Returns a specified number of contiguous elements from the start of a sequence.
[TakeWhile](#Take-/-TakeWhile) | IQueryable&lt;T&gt; | Returns elements from a sequence as long as a specified condition is true. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
[ThenBy](#simple-query.md#ordering-results) | IOrderedQueryable or IOrderedQueryable&lt;T&gt; | Performs a subsequent ordering of the elements in a sequence in ascending order according to a key.
[Where](simple-query.md#strongly-typed-linq-versus-dynamic-linq) | IQueryable or IQueryable&lt;T&gt; | Filters a sequence of values based on a predicate.

### Aggregate

You can use Sum, Average, Min or Max here.

Dynamic LINQ examples:

``` cs
var averagePrice = context.Orders.Aggregate("Average", "Price");
var maxAmount = context.Orders.Aggregate("Max", "Amount");
var minAmount = context.Orders.Aggregate("Min", "Amount");
var totalAmount = context.Orders.Aggregate("Sum", "Amount");
```

[Try it online](https://dotnetfiddle.net/2sF6Jw)

### All

Dynamic LINQ example to check if all orders have a price &gt; 2:

``` cs
bool allHavePriceGreaterThan2 = context.Orders.All("Price > 2");
```

[Try it online](https://dotnetfiddle.net/EUAzpU)

It's also possible to use the All method inside a dynamic query.

``` cs
var search = "e";
var stronglyTypedResult = context.Users.Where(u => u.Roles.All(r => r.Name.Contains(search)));
var dynamicResult = context.Users.Where("Roles.All(Name.Contains(@0))", search);
```

[Try it online](https://dotnetfiddle.net/XJ6y8a)

### Any

Dynamic LINQ example to check if any orders have a price &gt; 7:

``` cs
bool anyHavePriceGreaterThan7 = context.Orders.Any("Price > 7");
```

[Try it online](https://dotnetfiddle.net/DkIk94)

It's also possible to use the Any method inside a dynamic query.

``` cs
var search = "e";
var stronglyTypedResult = context.Users.Where(u => u.Roles.Any(r => r.Name.Contains(search)));
var dynamicResult = context.Users.Where("Roles.Any(Name.Contains(@0))", search);
```

[Try it online](https://dotnetfiddle.net/v8RiJQ)

### Average

Dynamic LINQ example to get the average price from all orders:

``` cs
var averagePriceExample1 = context.Orders.Select("Price").Average();
var averagePriceExample2 = context.Orders.Average("Price");
```

[Try it online](https://dotnetfiddle.net/IE9xLr)

### AsEnumerable

Just return a dynamic enumerable:

``` cs
var dynamicEnumerable = context.Orders.Select("Amount").AsEnumerable();
```

[Try it online](https://dotnetfiddle.net/eqyunf)

### Is,As,Cast,OfType

The `OfType` can be used with a Type or a fully qualified Type-name:

``` cs
var oftypeWorker = context.Employees.OfType(typeof(Worker));

// or

string boss = typeof(Boss).FullName;
var oftypeBoss = context.Employees.OfType(boss);
```

The `Is` can be used to determine if the entity has a specific type. In the example below, the number of bosses is counted:

``` cs
string boss = typeof(Boss).FullName;
int numberOfBosses = context.Employees.Count("is(@0)", boss);
```

With `As`, you can check if casting a class to a type is valid, or returns null.

``` cs
// Normal usage
var boss = entity as Boss;

// Dynamic usage
int useAsToCheckIftheTypeIsABoss = context.Employees.Count("As(@0) != null", boss);
```

The `Cast`-operator can be used to case a base-class to the real class. Note that this can only work if the cast is valid. Example:

``` cs
var allWorkers = context.Employees.OfType(typeof(Worker));
var castToWorkers = allWorkers.Cast(typeof(Worker));
```

[Try it online](https://dotnetfiddle.net/o2ydqi)

### Count

Here is an example of Dynamic LINQ for Count:

``` cs
int numberOfOrdersWhichHavePriceGreaterThan2 = context.Orders.Count("Price > 2");
var usersWhoHaveTwoRoles = context.Users.Where("u => u.Roles.Count() == 2");
```

[Try it online](https://dotnetfiddle.net/oXh4RO)

### DefaultIfEmpty

Here is an example of Dynamic LINQ for DefaultIfEmpty:

``` cs
var defaultIfEmpty = context.Customers.Where("Name == \"not-found\"").DefaultIfEmpty();
```

Or you can use this inside a Dynamic LINQ query:

``` cs
var users = context.Users.Select("Roles.Where(r => r.Name == \"Admin\").DefaultIfEmpty().FirstOrDefault()");
```

[Try it online](https://dotnetfiddle.net/JgVuOp)

### Distinct

Here is an example of Dynamic LINQ example for Distinct:

``` cs
IQueryable queryable = new[] { 1, 2, 2, 3 }.AsQueryable();
var distinctIntegerValues = queryable.Distinct();
```

Or you can use this inside a Dynamic LINQ query:

``` cs
var items = context.Customers
    .Include(c => c.Orders)
    .Select("new (Name as CustomerName, Orders.Distinct() as UniqueOrders)");
```

[Try it online](https://dotnetfiddle.net/j6lsQX)


### First / FirstOrDefault

The First and FirstOrDefault methods can be used directly on a `IQueryable` or `IQueryable<T>` like this:

``` cs
var first = context.Customers.First("c => c.City == \"Paris\"");

var firstOrDefault = context.Customers.FirstOrDefault("c => c.City == \"Otherworld\"");
```

Or you can use this inside a Dynamic LINQ query:

``` cs
var items = context.Users
    .Include(u => u.Roles)
    .Select("new (Name as userName, Roles.FirstOrDefault().Name as roleName)")
    .ToDynamicList();
```

[Try it online](https://dotnetfiddle.net/poY3Of)

### GroupBy

The Dynamic LINQ GroupBy functionality works the same as the normal, strongly typed GroupBy. Below are some examples in Dynamic LINQ.

#### GroupBy by a single Key

``` cs
var result = context.Posts.GroupBy("BlogId");
```

#### GroupBy by a composite Key

``` cs
var result = context.Posts.GroupBy("new (BlogId, PostDate)").OrderBy("Key.PostDate");
```

#### GroupBy by a single Key and with a single result

``` cs
var result = context.Posts.GroupBy("PostDate", "Title");
```

#### GroupBy by a single Key and a complex object result

``` cs
var result = context.Posts.GroupBy("PostDate", "new (Title, Content)");
```

#### GroupBy by a single Key and do a count()

``` cs
var result = context.Posts.GroupBy("BlogId").Select("new(Key, Count() AS Count)");
```

#### GroupBy by a single Key and do a sum()

``` cs
var result = context.Posts.GroupBy("BlogId").Select("new(Key, Sum(NumberOfReads) AS TotalReads)");
```

[See detailed examples here](https://dotnetfiddle.net/ppOOeo)

### GroupByMany

The GroupByMany extension method can only operate on a IEnumerable&lt;TElement&gt and can be used as a strongly typed method or as a Dynamic LINQ string expression.

#### GroupByMany strongly typed extension

``` cs
var sel = lst.AsQueryable().GroupByMany(x => x.Item1, x => x.Item2).ToList();
```

#### GroupByMany as a Dynamic LINQ string expression

``` cs
var sel = lst.AsQueryable().GroupByMany("Item1", "Item2").ToList();
```

For more examples, see [Try it online](https://dotnetfiddle.net/eLVc3b).

### GroupJoin

The Dynamic LINQ version from GroupJoin works the same as the normal strongly typed version of GroupJoin. Also, it supports joining on nullable keys (for both 'Left' and 'Right').

For more examples, see this [link](https://dotnetfiddle.net/sqkxpI).

### Join

Here is an example of a strongly typed Join:

``` cs
var realQuery = persons.Join(
    pets,
    person => person,
    pet => pet.Owner,
    (person, pet) => new { OwnerName = person.Name, Pet = pet.Name }
);
```

Here is the Dynamic LINQ counterpart for this Join:

``` cs 
var dynamicQuery = persons.AsQueryable().Join(
    pets,
    "it",
    "Owner",
    "new(outer.Name as OwnerName, inner.Name as Pet)"
);
```

[Try it online](https://dotnetfiddle.net/UlvbD8)

### Last / LastOrDefault

[Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
The Last and LastOrDefault methods can be used directly on a `IQueryable` or `IQueryable<T>` like this:

``` cs
var last = context.Customers.First("c => c.City == \"Paris\"");

var firstOrDefault = context.Customers.LastOrDefault("c => c.City == \"Otherworld\"");
```

Or you can use these inside a Dynamic LINQ query:

``` cs
var items = context.Users
    .Include(u => u.Roles)
    .Select("new (Name as userName, Roles.LastOrDefault().Name as roleName)")
    .ToDynamicList();
```

[Try it online](https://dotnetfiddle.net/Lq0MUv)

### Page / PageResult

If you want to get a list of sorted entities from a dynamic query you can use Page:

``` cs
var pagedCustomers = context.Customers.OrderBy("Name").Page(page, pageSize);
```

Or, if you want a PagedResult, which is useful for paging in grids, use this code:

``` cs
var result = context.Customers.OrderBy("Name").PageResult(page, pageSize);
```

Here is the PagedResult object:

``` cs
// The IQueryable version
public class PagedResult
{
    public IQueryable Queryable { get; set; }
    public int CurrentPage { get; set; }
    public int PageCount { get; set; }
    public int PageSize { get; set; }
    public int RowCount { get; set; }
}

// And the IQueryable<TSource> version
public class PagedResult<TSource> : PagedResult
{
    public new IQueryable<TSource> Queryable { get; set; }
}
```

Note that in both cases you need to sort the queryable, else you will get an Exception.

[Try it online](https://dotnetfiddle.net/b464OO)

### Reverse

Dynamic LINQ Example:

``` cs
var reversed = ((IQueryable) persons.AsQueryable()).Reverse();
```

[Try it online](https://dotnetfiddle.net/NAAuZJ)

### SelectMany

Here is how you can use SelectMany as extension method:

#### Use SelectMany as ExtensionMethod

``` cs
var result = context.Users.SelectMany("u => u.Roles.Select(r => r.Name)").ToDynamicArray();
```

#### Use SelectMany inside a Dynamic LINQ string and return a list of strings

``` cs
var result = context.Users.SelectMany("Roles.SelectMany(Permissions)").Select("Name");
```

#### Use SelectMany on Generic Type

``` cs
var result = context.Users.SelectMany<Permission>("Roles.SelectMany(Permissions)")
```

#### Use SelectMany with a Type

``` cs
var result = context.Users.SelectMany(typeof(Permission), "Roles.SelectMany(Permissions)")
```

[Try it online](https://dotnetfiddle.net/E76mjd)

### Skip / SkipWhile

Dynamic LINQ example to skip the first entity:

``` cs
var skipFirstCustomer = context.Customers.OrderBy("CustomerID").Skip(1);
```

Also, it is possible to use SkipWhile, to skip entities as long as the predicate does match:

``` cs
var skipped = context.Customers.ToList().AsQueryable().SkipWhile("CompanyName != \"ZZZ\"");
```

[Try it online](https://dotnetfiddle.net/kU8cOw)

[SkipWhile is not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)

### Sum

here is an example of Dynamic LINQ on how to get the total price from all orders:

``` cs
var totalPriceExample1 = context.Orders.Select("Price * Amount").Sum();
var var totalPriceExample2 = context.Orders.Sum("Price * Amount");
```

[Try it online](https://dotnetfiddle.net/DaE85i)

### Take / TakeWhile

Dynamic LINQ example to take some entities:

``` cs
var takeTwoCustomers = context.Customers.OrderBy("CustomerID").Take(2);
```

It's also possible to use TakeWhile, to take entities as long as the predicate does match:

``` cs
var takeWhile = context.Customers.ToList().AsQueryable().TakeWhile("CompanyName != \"ZZZ\"");
```

[Try it online](https://dotnetfiddle.net/cQVgON)

[TakeWhile is not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)


## Async Query Operators

The following table lists all Dynamic **Async** Query Operators which are supported on a `IQueryable` or `IQueryable<T>`.

Query Operator | Return Type | Info
- | - | -
AllAsync | Task&lt;bool&gt; | Asynchronously determines whether all the elements of a sequence satisfy a condition.
AnyAsync | Task&lt;bool&gt; | Asynchronously determines whether a sequence contains any elements.
AverageAsync | Task&lt;double&gt; | Asynchronously computes the average of a sequence of values that is obtained by invoking a projection function on each element of the input sequence.
CountAsync | Task&lt;int&gt; | Asynchronously returns the number of elements in a sequence.
FirstAsync | Task&lt;dynamic&gt; | Asynchronously returns the first element of a sequence.
FirstOrDefaultAsync | Task&lt;dynamic&gt; | Asynchronously returns the first element of a sequence, or a default value if the sequence contains no elements.
LastAsync | Task&lt;dynamic&gt; | Asynchronously returns the last element of a sequence. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
LastOrDefaultAsync | Task&lt;dynamic&gt; | Asynchronously returns the last element of a sequence, or a default value if the sequence contains no elements. [Maybe not supported in all scenarios](https://msdn.microsoft.com/library/bb738550.aspx)
LongCountAsync | Task&lt;long&gt; | Asynchronously returns the number of elements in a sequence.
SingleOrDefaultAsync | Task&lt;dynamic&gt; | Asynchronously returns the only element of a sequence that satisfies a specified condition or a default value if no such element exists.  This method throws an exception if more than one element satisfies the condition.
SumAsync | Task&lt;dynamic&gt; | Asynchronously computes the sum of a sequence of values.

### Notes

To use these extensions methods, you need to install another NuGet package which depends on **EntityFramework**, **Microsoft.EntityFrameworkCore** or **Z.EntityFramework.Classic**. See [Installation - NuGet](installation/nuget.md) for more details.