---
title: The Timestamp Attribute
description: Usage of the Timestamp Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/timestamp-attribute
status: Published
lastmod: 2025-07-11
---

# EF Core Timestamp Attribute

The `Timestamp` attribute is used to specify that a property should take part in [concurrency management](/concurrency).

The `Timestamp` attribute is only valid if applied to a property that is a byte array. How this is implemented is dependent on the current database provider.  In SQL Server, this will map to a `rowversion` type.  

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
    [Timestamp]
    public byte[] RowVersion { get; set; }
}
```

#### Fluent API
The Fluent API equivalent to the `Timestamp` attribute is the [IsConcurrencyToken method](/configuration/fluent-api/isconcurrencytoken-method) chained with the [ValueGeneratedOnAddOrUpdate method](/configuration/fluent-api/valuegeneratedonaddorupdate-method). 

From version 1.1 of EF Core, The [IsRowVersion method](/configuration/fluent-api/isrowversion-method) will also be available which acts as a convenience wrapper for the chained combination described above.

#### Further Reading
- [Concurrency Management](/concurrency)

<!--https://github.com/aspnet/EntityFramework/pull/6408
http://stackoverflow.com/questions/6167265/what-fluent-api-method-is-correspond-to-timestamp-attribute-in-data-annotation
https://github.com/aspnet/EntityFramework/issues/5554-->
