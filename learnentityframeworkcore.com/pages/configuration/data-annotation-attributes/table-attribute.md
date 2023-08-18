---
title: The Table Attribute
description: Usage of the Table Attribute in Entity Framework Core
canonical: /configuration/data-annotation-attributes/table-attribute
status: Published
lastmod: 2023-02-28
---

# EF Core Table Attribute

The `Table` attribute is applied to an entity to specify the name of the database table that the entity should map to. The following example specifies that the `Book` entity should map to a database table named `tbl_Book`:

```csharp
[Table("tbl_book")]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

If the table that the entity maps to belongs to a [non-default schema](/conventions#schema), the `Table` attribute provides a way to specify the name of the schema through the `Schema` property:

```csharp
[Table("Books", Schema="library")]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```

#### Fluent API

The Fluent API equivalent to the `Table` attribute is the [ToTable method](/configuration/fluent-api/totable-method).
