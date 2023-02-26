---
PermaID: 1000128
Title: Entity Framework Column Data Annotations
MetaDescription: Unlock the power of Entity Framework by using the Column Data Annotations. Learn how to set this attribute to specify the column name.
LastMod: 2023-02-24
Tags: code-first fluent-api data-annotations
---

# Entity Framework Column Data Annotations

In EF 6, the default convention creates a creates a column name similar to the property name in an entity class.

 - It is also the same as `Table` attribute, but `Table` attribute overrides the table behavior while `Column` attribute overrides the column behavior.
 - It will create a column in a database with a specified name in the `Column` attribute for a given property.

The following example maps the `BookTitle` property in the Book entity to a database column named `Title`.

```csharp
public class Book
{
    public int BookId { get; set; }
    [Column("Title")]
    public string BookTitle { get; set; }
}
```

[Try it](https://dotnetfiddle.net/Rh3pAR)