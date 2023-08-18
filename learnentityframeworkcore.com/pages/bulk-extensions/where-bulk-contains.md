---
title: EF Core Where Bulk Contains
description: The WhereBulkContains method let you filter a LINQ query by including all items from an existing list.
canonical: /bulk-extensions/where-bulk-contains
status: Published
lastmod: 2023-03-02
---

# EF Core Where Bulk Contains

[Entity Framework Extensions](https://entityframework-extensions.net/) provides the `WhereBulkContains` extension method that allows you to filter a LINQ query by including all items from an existing list.

For example, you have a list of `Customer` with the `CustomerID` and a few other properties populated. Then you want to retrieve those authors from the database to update those properties.

The `WhereBulkContains` method will filter entities to include those contained in the list.

```csharp
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
```

## Note

 - NuGet Package: [https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore](https://www.nuget.org/packages/Z.EntityFramework.Extensions.EFCore)
 - Documentation: [Entity Framework Extensions – WhereBulkContains](https://entityframework-extensions.net/where-bulk-contains)