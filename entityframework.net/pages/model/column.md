---
PermaID: 1000128
Name: Column
---

# Column

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