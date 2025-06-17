---
PermaID: 1000115
Title: Entity Framework Table Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the Table Data Annotations. Learn how to set this attribute to specify the table name and schema name.
LastMod: 2025-06-17
Tags: code-first fluent-api data-annotations
---

# Entity Framework Table Data Annotation

In EF 6, the default convention creates a table name matching with `<DbSet<TEntity>` property name + 's' or 'es' in a context class.

- The `Table` attribute overrides this default convention.
- It is applied to an entity to specify the name of the database table that the entity should map to. 

The following example specifies that the `Book` entity should map to a database table named `BooksInfo`.

```csharp
[Table("BooksInfo")]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/vK2uTI)

If the table that the entity maps to belongs to a non-default schema, the Table attribute provides a way to specify the name of the schema through the Schema property.

```csharp
[Table("BooksInfo", Schema="lib")]
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/TyNs1h)