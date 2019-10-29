---
PermaID: 1000161
Name: DataReader already open
---

# DataReader already Open

## Exception: There is already an open DataReader associated with this Command which must be closed first

When executing the following code.

```csharp
foreach (var blog in blogs)
{
    blog.Name += " updated";
    blog.Url += " updated";
    context.SaveChanges();
}
```
It gives the following exception.

`InvalidOperationException: There is already an open DataReader associated with this Command which must be closed first.`

### StackOverflow Related Questions

 - [There is already an open DataReader associated with this Command which must be closed first](https://stackoverflow.com/questions/6062192/there-is-already-an-open-datareader-associated-with-this-command-which-must-be-c)

## Solution

This can happen if you execute a query while iterating over the results from another query.

### Solution 1

 - This can be easily solved by allowing MARS in your connection string. 
 - Add `MultipleActiveResultSets=true` to the provider part of your connection string.

```csharp
<connectionStrings>
    <add name="BloggingContextDb" connectionString="Data Source=(localdb)\ProjectsV13;Initial Catalog=BloggingContextDb;
        MultipleActiveResultSets=true;" providerName="System.Data.SqlClient" />
</connectionStrings>
```

 - Enabling MARS should only be done for a very small subset of problems/use-cases. 
 - In most cases, the error is caused by BAD CODE within the calling application. 

### Solution 2

Adding .ToList() to the collection using in foreach loop will likely solve the problem.


```csharp
var blogs = blogs.ToList();
foreach (var blog in blogs)
{
    blog.Name += " updated";
    blog.Url += " updated";
    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/F2zQna)

### Solution 3

Call SaveChanges() outside foreach loop


```csharp
foreach (var blog in blogs)
{
    blog.Name += " updated";
    blog.Url += " updated";
}
context.SaveChanges();
```
[Try it online](https://dotnetfiddle.net/WhDkGy)