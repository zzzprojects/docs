---
Title: EF Core GetTagWithValues and GetTagWithCallSiteValues
MetaDescription: Learn how to retrieve TagWith and TagWithCallSite values from an EF Core query while preserving the order in which the tags were added.
LastMod: 2026-08-17
---

# EF Core GetTagWithValues and GetTagWithCallSiteValues

EF Core provides [query tags](https://learn.microsoft.com/en-us/ef/core/querying/tags) to help correlate LINQ queries in your code with the generated SQL captured in logs.

EF Core provides two methods for adding query tags:

* [TagWith](https://learn.microsoft.com/en-us/ef/core/querying/tags): Adds a string tag to a query.
* [TagWithCallSite](https://learn.microsoft.com/en-us/ef/core/querying/tags#tagging-with-file-name-and-line-number): Adds the file name and line number where the LINQ query is defined in the source code.

These methods make it easy to add tags, but EF Core does not provide a simple way to retrieve their values from an `IQueryable`.

Our library provides two utility methods:

* `GetTagWithValues`: Returns all string values added with `TagWith`.
* `GetTagWithCallSiteValues`: Returns all file paths and line numbers added with `TagWithCallSite` as a list of `TagWithCallSiteInfo`.

Both methods preserve the order in which the tags were added to the query.

## GetTagWithValues Example

To retrieve all values added with `TagWith`, call `GetTagWithValues` on your `IQueryable` query.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

using (var context = new EntityContext())
{
    var query = context.EntitySimples
        .TagWith("Tag #1")
        .TagWith("Tag #2");

    List<string> tags = query.GetTagWithValues();
}
```

The `tags` variable contains `"Tag #1"` followed by `"Tag #2"`, preserving the order in which they were added to the query.

## GetTagWithCallSiteValues Example

To retrieve all file paths and line numbers added with `TagWithCallSite`, call `GetTagWithCallSiteValues` on your `IQueryable` query.

```csharp
// @nuget: Z.EntityFramework.Plus.EFCore
using Z.EntityFramework.Plus;

using (var context = new EntityContext())
{
    var query = context.EntitySimples
        .TagWithCallSite("FilePath #1")
        .TagWithCallSite(filePath: "FilePath #2", lineNumber: 2)
        .TagWithCallSite(lineNumber: 3);

    List<TagWithCallSiteInfo> callSites =
        query.GetTagWithCallSiteValues();
}
```

The `callSites` variable contains one item for every `TagWithCallSite` call, preserving the order in which they were added to the query.

Every returned item is a `TagWithCallSiteInfo` with the following definition:

```csharp
public class TagWithCallSiteInfo
{
    public string FilePath { get; set; }
    public int LineNumber { get; set; }
}
```

The `FilePath` and `LineNumber` properties contain the values associated with each `TagWithCallSite` call.

## Summary

EF Core query tags help you identify LINQ queries in the generated SQL and application logs.

You can use:

* `GetTagWithValues` to retrieve all string values added with `TagWith`.
* `GetTagWithCallSiteValues` to retrieve all file paths and line numbers added with `TagWithCallSite`.

Both methods preserve the order in which the tags were added. They are useful when your code needs to inspect query tags and apply custom logic before the query is executed.
