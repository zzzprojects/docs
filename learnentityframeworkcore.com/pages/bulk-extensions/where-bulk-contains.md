---
title: EF Core WhereBulkContains with Entity Framework Extensions  
description: Learn how to use the `WhereBulkContains` method from Entity Framework Extensions to retrieve entities from your database that match a list of IDs, GUIDs, or even full entities.  
canonical: /bulk-extensions/where-bulk-contains  
status: Published  
lastmod: 2025-07-23
---

# EF Core WhereBulkContains with Entity Framework Extensions

The EF Core [`WhereBulkContains`](https://entityframework-extensions.net/where-bulk-contains) method from the [Entity Framework Extensions](https://entityframework-extensions.net) library allows you to filter a LINQ query by including all items from an existing list.

For example, imagine you have a list of `Customer` objects where the `CustomerID` and a few other properties are already populated. You want to retrieve those customers from the database so you can update their values.

The `WhereBulkContains` method lets you filter entities based on what’s in your list:

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

    // context.SaveChanges();
    context.BulkSaveChanges();
}
```

This method also works with batch operations like [DeleteFromQuery](https://entityframework-extensions.net/delete-from-query) and [UpdateFromQuery](https://entityframework-extensions.net/update-from-query), making it even easier to use in those scenarios:

```csharp
// @nuget: Z.EntityFramework.Extensions.EFCore
using Z.EntityFramework.Extensions;

var dictDeserializedCustomers = deserializedCustomers.ToDictionary(x => x.CustomerID);

using (var context = new EntityContext())
{
    context.Customers.WhereBulkContains(deserializedCustomers).DeleteFromQuery();

    context.Customers
           .WhereBulkContains(deserializedCustomers)
           .UpdateFromQuery(x => new Customer { IsActive = false });
}
```

This unique method is part of a professional library made by [ZZZ Projects](https://zzzprojects.com/). Entity Framework Extensions is the most popular and complete EF Core library available, with over **50 million downloads** and more than **5,000 paid customers**.

---

## 🚀 Why Use the `WhereBulkContains` Method from Entity Framework Extensions?

Most developers use the standard `Where` + `Contains` combo to filter data in EF Core.

It works fine… until it doesn’t. Here are some common limitations:

* ❌ Only supports basic types like `int` or `Guid`
* ❌ Limited by SQL parameter count (you can’t pass too many IDs)
* ❌ Doesn’t support composite keys or other complex scenarios

That’s where the `WhereBulkContains` method from Entity Framework Extensions really shines — it was built to overcome all of these limitations:

* ✅ Supports **any kind of data source**

  * ✅ Basic types like `List<int>` or `List<Guid>`
  * ✅ Entity types like `List<Customer>`
  * ✅ Anonymous types (e.g., `new { ID = 1 }`)
  * ✅ Even `ExpandoObject` lists
* ✅ Handles an **unlimited number of items**
* ✅ Works with **surrogate keys** and **composite keys**

No matter how complex your list is, `WhereBulkContains` makes filtering just work — and scale.

---

## 📦 How to Install Entity Framework Extensions

To use the `WhereBulkContains` method, install the [Z.EntityFramework.Extensions.EFCore NuGet package](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore/):

```powershell
PM> NuGet\Install-Package Z.EntityFramework.Extensions.EFCore
```

```bash
> dotnet add package Z.EntityFramework.Extensions.EFCore
```

Entity Framework Extensions uses [EF Core pinned versioning](https://entityframework-extensions.net/efcore-pinned-versioning), so make sure the **first digit** of the package version matches your EF Core version. For example:

* If you're using `EF Core 9`, use version `v9.x.y.z`
* If you're using `EF Core 8`, use version `v8.x.y.z`
* And so on...

Looking for a different version, like `WhereBulkContains` for EF6?
👉 [Download it here](https://entityframework-extensions.net/download)

---

## ✅ Final Thoughts

When it comes to filtering large datasets in EF Core, the `WhereBulkContains` method from Entity Framework Extensions is a powerful tool you shouldn’t overlook.

It works with any data source, supports unlimited items, and handles complex scenarios that the standard `Where` + `Contains` method just can’t. Whether you're working with basic ID lists or full entities, this method helps you write cleaner, faster, and more efficient queries.

Give it a [try](https://entityframework-extensions.net/where-bulk-contains) and see how much smoother your data filtering can be!

---

## 🔗 External Links

Looking for more resources? Here are some helpful links to learn more about the `WhereBulkContains` method and **Entity Framework Extensions**, explore its documentation, and download the right version for your project.

### 📘 Documentation

* 🔗 [Where Bulk Contains – Official Documentation](https://entityframework-extensions.net/where-bulk-contains)
* 🔗 [Entity Framework Extensions – Overview](https://entityframework-extensions.net/)

### 📦 Downloads & Packages

* 🔗 [NuGet Package – Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
* 🔗 [Download Page – All Versions (EF6, EF Core, etc.)](https://entityframework-extensions.net/download)