---
PermaID: 1000114
Title: Entity Framework Timestamp Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the Timestamp Data Annotations. Learn how to set this attribute to specify timestamp columns or row versions.
LastMod: 2023-02-25
Tags: code-first fluent-api data-annotations
---

# Entity Framework Timestamp Data Annotation

The `TimeStamp` attribute is used to creates a column with timestamp data type in the SQL Server database.

 -  EF Code first will treat `Timestamp` properties the same as ConcurrencyCheck properties.
 -  It can only be applied once in an entity class to a byte array type property. 
 -  It will also ensure that the database field that code first generates is non-nullable. 
 -  Entity Framework API automatically uses this `Timestamp` column in concurrency check on the `UPDATE` statement in the database.

In the following example, the `Timestamp` attribute is applied to the `RowVersion` property which is a byte array.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    [Timestamp]
    public byte[] RowVersion { get; set; }
}
```

[Try it](https://dotnetfiddle.net/HedUXa)