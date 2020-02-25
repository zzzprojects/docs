# LINQ Dynamic

## Description
You can execute query dynamically through free extensions provided by the library [Eval-Expression.NET](http://eval-expression.net/).

This feature is available for free in the EF Classic Community.

## Predicate

All LINQ predicate methods are supported.

```csharp
var list = context.Customers.WhereDynamic(x => "x.IsActive").ToList();
var list2 = context.Customers.WhereDynamic(x => "x.IsActive == IsActive", new { IsActive = false }).ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/Exa0zS) | [NET Framework](https://dotnetfiddle.net/GTttpq)

###### Deferred Methods
| Name | Description | Example |
| :--- | :---------- | :------ |
| `OrderByDescendingDynamic` | Sorts the elements of a sequence in descending order using a dynamic expression. | [NET Core](https://dotnetfiddle.net/zt3MEa) / [NET Framework](https://dotnetfiddle.net/doNrVQ) |
| `OrderByDynamic` | Sorts the elements of a sequence in ascending order using a dynamic expression. | [NET Core](https://dotnetfiddle.net/9GvILu) / [NET Framework](https://dotnetfiddle.net/rzKycR) |
| `SelectManyDynamic` | Projects each element of a sequence to an IEnumerable<T> and flattens the resulting sequences into one sequence using a dynamic expression. | [NET Core](https://dotnetfiddle.net/toMh6j) / [NET Framework](https://dotnetfiddle.net/KLF5e7) |
| `SelectDynamic` | Projects each element of a sequence into a new form using a dynamic expression. | [NET Core](https://dotnetfiddle.net/X9uPDb) / [NET Framework](https://dotnetfiddle.net/YE83om) |
| `SkipWhileDynamic` | Skip all elements until the predicate is not satisfy. | |
| `TakeWhileDynamic` | Take all elements until the predicate is not satisfy. | |
| `ThenByDescendingDynamic` | Performs a subsequent ordering of the elements in a sequence in descending order using a dynamic expression. | [NET Core](https://dotnetfiddle.net/Kd2WQY) / [NET Framework](https://dotnetfiddle.net/8FxroD) |
| `ThenByDynamic` | Performs a subsequent ordering of the elements in a sequence in ascending order using a dynamic expression. | [NET Core](https://dotnetfiddle.net/9pyiEV) / [NET Framework](https://dotnetfiddle.net/pVCcRf) |
| `WhereDynamic` | Filters a sequence of values based on a predicate using a dynamic expression. | [NET Core](https://dotnetfiddle.net/z8t5wV) / [NET Framework](https://dotnetfiddle.net/QhVfRW) |

###### Immediate Methods
| Name | Description | Example |
| :--- | :---------- | :------ |
| `AllDynamic` | Determines whether all elements of a sequence satisfy a condition using a dynamic expression. | [NET Core](https://dotnetfiddle.net/XrG83V)  / [NET Framework](https://dotnetfiddle.net/YCT73M) |
| `AnyDynamic` | Determines whether any element of a sequence exists or satisfies a condition using a dynamic expression. | [NET Core](https://dotnetfiddle.net/Gh9OSM) / [NET Framework](https://dotnetfiddle.net/vEbwLr) |
| `CountDynamic` | Returns the number of elements in a sequence using a dynamic expression. | [NET Core](https://dotnetfiddle.net/ox7EFW) / [NET Framework](https://dotnetfiddle.net/v8rqKV) |
| `FirstDynamic` | Returns the first element of a sequence using a dynamic expression. | [NET Core](https://dotnetfiddle.net/gW1CqX) / [NET Framework](https://dotnetfiddle.net/CfxUKL) |
| `FirstOrDefaultDynamic` | Returns the first element of a sequence, or a default value if no element is found using a dynamic expression. | [NET Core](https://dotnetfiddle.net/3ZlZuq) / [NET Framework](https://dotnetfiddle.net/UX3Ymb) |
| `LongCountDynamic` | Returns an Int64 that represents the number of elements in a sequence using a dynamic expression. | [NET Core](https://dotnetfiddle.net/fc6TLH) / [NET Framework](https://dotnetfiddle.net/4xrM1d) |
| `SingleDynamic` | Returns a single, specific element of a sequence using a dynamic expression. | [NET Core](https://dotnetfiddle.net/SHPNY8)  / [NET Framework](https://dotnetfiddle.net/onW4hW) |
| `SingleOrDefaultDynamic` | Returns a single, specific element of a sequence, or a default value if that element is not found using a dynamic expression. | [NET Core](https://dotnetfiddle.net/S07cJB) / [NET Framework](https://dotnetfiddle.net/nU97uw) |

## Execute

The Execute method is the LINQ Dynamic ultimate methods which let you evaluate and execute a dynamic expression and return the result.

```csharp
var list = context.Customers.Execute<IEnumerable<Customer>>("Where(x => x.IsActive == true)").ToList();
var list2 = context.Customers.Execute<IEnumerable<Customer>>("Where(x => x.IsActive == IsActive)", new { IsActive = false }).ToList();
```
Try it: [NET Core](https://dotnetfiddle.net/u2HVih) / [NET Framework](https://dotnetfiddle.net/7S3JS0)

###### Methods
| Name | Description | Example |
| :--- | :---------- | :------ |
| `Execute` | Execute LINQ dynamic using an expression. | [NET Core](https://dotnetfiddle.net/FU2FsS) / [NET Framework](https://dotnetfiddle.net/z1jIkv) |
| `Execute<TResult>` | Execute LINQ dynamic using an expression. | [NET Core](https://dotnetfiddle.net/YgaB4Y) / [NET Framework](https://dotnetfiddle.net/jgOyFi) |
