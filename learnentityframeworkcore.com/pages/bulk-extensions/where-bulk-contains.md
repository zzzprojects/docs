---
title: EF Core WhereBulkContains with Entity Framework Extensions
description: Learn how to use the WhereBulkContains method from Entity Framework Extensions to retrieve entities from your database that match a list of IDs, GUIDs, or even full entities. Created by ZZZ Projects, this method is optimized for performance and works with any kind of key or data source.
canonical: /bulk-extensions/where-bulk-contains
status: Published
lastmod: 2025-07-23
---

# EF Core WhereBulkContains with Entity Framework Extensions

The EF Core [`WhereBulkContains`](https://entityframework-extensions.net/where-bulk-contains) extension method from Entity Framework Extensions allows you to filter a LINQ query by including all items from an existing list.

For example, imagine you have a list of `Customer` objects with the `CustomerID` and a few other properties already populated. You want to retrieve those customers from the database to update their values.

The `WhereBulkContains` method lets you filter entities based on what’s in your list.

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var dictDeserializedCustomers = deserializedCustomers.ToDictionary(x => x.CustomerID);

using (var context = new EntityContext())
{
    var customers = context.Customers.WhereBulkContains(deserializedCustomers).ToList();

    customers.ForEach(x =>
    {
        var deserializedCustomer = dictDeserializedCustomers[x.CustomerID];

        x.Code = deserializedCustomer.Code;
        x.Email = deserializedCustomer.Email;
        x.FirstName = deserializedCustomer.FirstName;
        x.LastName = deserializedCustomer.LastName;
    });

    context.BulkSaveChanges();
}
````

## 🚀 Why Use the WhereBulkContains Method from Entity Framework Extensions?

EF Core already lets you filter your database using the LINQ `Where` + `Contains` method.
However, this method has some limitations—for example, it only works well with basic ID lists.

The `WhereBulkContains` method removes those limitations and lets you use any kind of data source:

* ✅ Basic types like `List<int>` and `List<Guid>`
* ✅ Entity types like `List<Customer>`
* ✅ Anonymous types
* ✅ ExpandoObject lists

It also comes with additional advantages over the regular `Where` + `Contains` method:

* ✅ Supports an unlimited number of items
* ✅ Supports surrogate keys

## 📦 How to Install Entity Framework Extensions

To use the `WhereBulkContains` extension method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Make sure the **first digit** of the version matches your EF Core version.
For example, if you're using `EF Core 9`, use version `v9.x.y.z` of the package.

Learn more about [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning).

Need another version like EF6? Visit the official downloads page:
👉 [https://entityframework-extensions.net/download](https://entityframework-extensions.net/download)

---

## 🔗 External Links

Here are some useful links to help you get started with **Entity Framework Extensions** and its **Bulk Extension methods**, like `WhereBulkContains`:

* 🔗 [WhereBulkContains Documentation](https://entityframework-extensions.net/where-bulk-contains)
* 🔗 [Entity Framework Extensions Overview](https://entityframework-extensions.net/)
* 🔗 [NuGet Package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [ZZZ Projects (Official Site)](https://zzzprojects.com/)
