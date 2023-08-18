---
title: The ConcurrencyCheck Attribute
description: Usage of the ConcurrencyCheck attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/concurrencycheck-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core ConcurrencyCheck Attribute

The `ConcurrencyCheck` attribute is used to specify that a property should be included in a `WHERE` clause in an `UPDATE` or `DELETE` statement as part of [concurrency management](/concurrency).

```csharp
public class Author
{
    public int AuthorId { get; set; }
    [ConcurrencyCheck]
    public string FirstName { get; set; }
    [ConcurrencyCheck]
    public string LastName { get; set; }
    public string Biography { get; set; }
    public ICollection<Book> Books { get; set; }
}
```
#### Fluent API
The Fluent API equivalent to the `ConcurrencyCheck` attribute is the [IsConcurrencyToken method](/configuration/fluent-api/isconcurrencytoken-method).
