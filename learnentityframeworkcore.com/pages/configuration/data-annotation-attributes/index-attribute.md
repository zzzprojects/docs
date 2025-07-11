---
title: The Index attribute in Entity Framework Core
description: The Entity Framework Core `IndexAttribute` was introduced in .NET 5 and is used to create a database index on the column mapped to the specified entity property.
canonical: /configuration/data-annotation-attributes/index-attribute
status: Published
lastmod: 2025-07-11
---

# EF Core Index Attribute

The Entity Framework Core `IndexAttribute` was introduced in .NET 5 and is used to create a database index on the column mapped to the specified entity property. By default, indexes are created for foreign keys and alternate keys. You may wish to create indexes on other properties to speed up data retrieval. The following example sets a non-unique index on the `Isbn` column:

```csharp
[Index(nameof(Isbn))]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
```
### Unique Index

To create a unique index, you must specify `IsUnique=true`:
```csharp
[Index(nameof(Isbn), IsUnique=true)]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
```

### Multi-Column Index

If you wish to apply an index on more than one column, you specify them as a comma separated value.

```csharp
[Index(nameof(Ssn),nameof(DateOfBirth))]
public class Patient
{
    public int PatientId { get; set; }
    public string Ssn { get; set; }
    public DateTime DateOfBirth { get; set; }
}
```
### Index Name

The name of the generated index follows the pattern IX_<type name>_<property name> by default. You can alter this using the Name property of the Index attribute:

```csharp
[Index(nameof(Isbn), IsUnique=true, Name="Unique_Isbn")]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public string Isbn { get; set; }
}
```

### Fluent API
The Fluent API equivalent method for the `Index` attribute is the [HasIndex](/configuration/fluent-api/hasindex-method) method