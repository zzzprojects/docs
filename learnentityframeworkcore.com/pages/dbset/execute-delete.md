---
title: ExecuteDelete in EF Core – A Faster Way to Delete Entities
description: Discover how to use the new ExecuteDelete method starting from EF Core 7 to delete entities more efficiently—no tracking, no SaveChanges needed.
canonical: /dbset/execute-delete
status: Published
lastmod: 2025-08-22
---

# EF Core Execute Delete

The traditional way to delete entities in EF Core looks like the following code:

```csharp
using (var context = new PackageContext())
{
    var packages = context.Packages.Where(x => x.AuthorName.Contains("ZZZ Projects"));
    context.RemoveRange(packages);
    var affectedRows = context.SaveChanges();
}
```

You first retrieve all packages created by ZZZ Projects, tell the change tracker that you want to delete them, and finally delete them.

While this code works perfectly, it's also a very inefficient way to delete entities in many scenarios:

- ❌ You load unnecessary data from the database into your application (materializing data)
- ❌ You start tracking entities that will be deleted anyway
- ❌ Memory inefficient (just imagine loading 10,000 entities for nothing!)
- ❌ You need several batches when deleting thousands of entities

## ExecuteDelete and ExecuteDeleteAsync

EF Core 7 introduced the `ExecuteDelete` and `ExecuteDeleteAsync` methods ([official documentation](https://learn.microsoft.com/en-us/ef/core/saving/execute-insert-update-delete#executedelete)) to let you delete entities from your database in a much more efficient way. Even though EF Core 7 added these methods, similar functionality was already available since the early days of EF Core through the [DeleteFromQuery](https://entityframework-extensions.net/delete-from-query) method from the [Entity Framework Extensions](https://entityframework-extensions.net/) library.

Let’s now see how the `ExecuteDelete` method works by deleting all packages created by ZZZ Projects.

```csharp
using (var context = new LibraryContext())
{
    var affectedRows = context.Packages.Where(x => x.AuthorName.Contains("ZZZ Projects")).ExecuteDelete();
}
```

Now, the same task can be done in one line instead of three. The syntax is also much easier to read.

### 🔑 Key Differences

Let’s now see the main differences between the traditional way to delete entities and the new `ExecuteDelete` method:

- 💡 **Immediate Effect:** You don’t need to call `SaveChanges`
- ✅ You don’t load entities before deleting them
- ✅ SQL is executed directly in the database in a single roundtrip
- ✅ Less code to write, more readable code
- ❌ Any tracked entities won't be kept in sync with the context
- ❌ Commands must be sent in the correct order to avoid database constraint violations (e.g., deleting dependents before principals)

All this means that the `ExecuteDelete` method cannot replace the traditional way in every scenario. It's a method that complements, rather than replaces, the existing `SaveChanges` approach.

## More Examples

### Basic case
The following example shows how to call `ExecuteDelete` on a `DbSet`, which will immediately delete all entities from that `DbSet` in the database.

For example, to delete all authors:

```csharp
using (var context = new LibraryContext())
{
    context.Authors.ExecuteDelete();
}
```

This generates the following SQL statement:

```sql
DELETE FROM [a]
FROM [Authors] AS [a]
```

### Basic case with filter

You can also use filters in your query:

```csharp
using (var context = new LibraryContext())
{
    context.Authors.Where(a => a.Name.Contains("ZZZ Projects")).ExecuteDelete();
}
```

Which generates:

```sql
DELETE FROM [a]
FROM [Authors] AS [a]
WHERE [a].[Name] LIKE N'%ZZZ Projects%'
```

### Complex filter

More complex filters are also supported, including filtering based on related data. For example, to delete tags only from old blog posts:

```csharp
using (var context = new BloggingContext())
{
    context.Tags
        .Where(t => t.Posts.All(e => e.PublishedOn.Year < 2018))
        .ExecuteDeleteAsync();
}
```

Which executes the following SQL:

```sql
DELETE FROM [t]
FROM [Tags] AS [t]
WHERE NOT EXISTS (
    SELECT 1
    FROM [PostTag] AS [p]
    INNER JOIN [Posts] AS [p0] ON [p].[PostsId] = [p0].[Id]
    WHERE [t].[Id] = [p].[TagsId] AND NOT (DATEPART(year, [p0].[PublishedOn]) < 2018))
```

### Return the number of rows affected

You can also check the number of affected rows to make sure at least one row was deleted:

```csharp
var authorName = "ZZZ Projects";
using (var context = new LibraryContext())
{
    var affectedRows = context.Authors.Where(a => a.Name.Contains(authorName)).ExecuteDelete();
    
    if (affectedRows == 0)
    {
        throw new Exception($"Oops! No authors with the name '{authorName}' were found.");
    }
}
```

## Is ExecuteDelete a real “Bulk Delete”?

Even though many call it a **bulk delete**, `ExecuteDelete` is not the same as a [real bulk delete](/bulk-extensions/bulk-delete) operation such as the one provided by [Entity Framework Extensions](https://entityframework-extensions.net/bulk-delete).

`ExecuteDelete` is a **set-based SQL shortcut**: it generates a single `DELETE FROM … WHERE …` statement and executes it immediately, without loading entities or using the change tracker. That’s very efficient, but it still differs significantly from a high-throughput bulk delete pipeline.

### Key Differences

| Aspect            | `ExecuteDelete` (EF Core)                       | Real Bulk Delete (Entity Framework Extensions)                              |
| ----------------- | ----------------------------------------------- | --------------------------------------------------------------------------- |
| How it works      | Direct `DELETE FROM` SQL with LINQ filter       | Loads entity keys into a staging table, then issues optimized `DELETE JOIN` |
| Per-row control   | ❌ Same filter applies to all rows               | ✅ Delete a custom list of entities, each row matched individually           |
| Performance scale | ✅ Fast for set-based deletes                    | 🚀 Optimized for millions of rows (batching,  bulk copy)               |
| Related entities  | ❌ Must handle order yourself (dependents first) | ✅ `IncludeGraph` to cascade through relationships safely                    |
| Audit/logging     | ❌ No hooks                                      | ✅ Pre-/Post- events, logging integration                                    |
| Advanced options  | ❌ None                                          | ✅ Batch size, transaction control, identity/key handling, retries           |
| Entity sync       | ❌ Tracked entities not updated                  | ✅ Can sync changes back if needed                                           |

---

### A Simple Mental Model

* **`ExecuteDelete`** = “Tell the database: *delete all rows that match this condition*.”
  Great for **uniform deletes** (e.g., remove all expired sessions).

* **Real Bulk Delete** = “Take this list of entities I already have in memory, push their keys to the database efficiently, and delete **exactly those rows**.”
  Crucial when you have **specific records** to remove or are working with **millions of rows**.

---

### Example: Deleting Specific Entities

Suppose you need to delete **1,000,000 customers by ID** from a CSV import.

With `ExecuteDelete`, you would need to loop in chunks, manually create filters, or stage data yourself.

With EF Extensions:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

// Delete exactly the customers in this list
var customersToDelete = GetCustomersFromCsv();
context.BulkDelete(customersToDelete, options =>
{
    options.BatchSize = 10000; // fine-tune performance
});
```

Behind the scenes, EF Extensions pushes your IDs into a temporary table and runs a **high-throughput `DELETE JOIN`**—something `ExecuteDelete` cannot do on its own.

---

### When to Use Which?

* Use **`ExecuteDelete`** when:

  * You want to remove rows that all match a **filter condition**.
  * You want a **native EF Core solution** with no extra library.
  * The dataset is moderate and you don’t need advanced options.

* Use a **real bulk delete** (Entity Framework Extensions) when:

  * You need to delete a **specific list of entities** with unique IDs/values.
  * You’re working with **very large datasets** (hundreds of thousands or millions of rows).
  * You require advanced features like **graph deletes, batching, auditing, or logging**.

---

## External Resource

### Entity Framework 7 – Bulk Editing

<iframe width="560" height="315" src="https://www.youtube.com/embed/A5_thTxsCjY?si=IhIioXL8AplArLl1" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

_by @CodingTutorialsAreGo ([Jasper Kent](/contributors/jasper-kent))_

In this video, Jasper introduces the new `ExecuteDelete` feature added in EF Core 7. He walks you through every step—from how deletions were traditionally performed to replacing them with the new method for both single and multiple entities. He also compares the SQL generated, explains why calling `SaveChanges` is no longer required, and shows how to retrieve the number of affected rows.

He covers everything from project setup, traditional pitfalls, and how this new method improves performance and code clarity.

**🕒 Key timestamps in the video:**
1. **[00:00 - Introduction](https://youtube.com/watch?v=A5_thTxsCjY&t=0)** – Overview of `ExecuteDelete` and `ExecuteUpdate`.
2. **[00:34 - Project Setup](https://youtube.com/watch?v=A5_thTxsCjY&t=34)** – Quick explanation of the demo project.
3. **[02:09 - Traditional Way to Delete](https://youtube.com/watch?v=A5_thTxsCjY&t=129)** – Shows why the old method is inefficient.
4. **[07:19 - Implementing ExecuteDelete](https://youtube.com/watch?v=A5_thTxsCjY&t=439)** – Demo of the new method and SQL comparison.
5. **[24:35 - Conclusion and Performance Insights](https://youtube.com/watch?v=A5_thTxsCjY&t=1475)** – Final thoughts on how game-changing these new methods are.

You can download the project source code here: [Ef7-Bulk-Actions](https://github.com/JasperKent/Ef7-Bulk-Actions)

## Conclusion

This is definitely a method you want to master. Most of your deletes should now use `ExecuteDelete`. However, keep in mind that despite the method name, it’s often referred to as *bulk delete*, which creates confusion with the [BulkDelete](https://entityframework-extensions.net/bulk-delete) feature from the Entity Framework Extensions library—these are different things with different behaviors.