---
PermaID: 1000117
Title: Entity Framework Required Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the Required Data Annotations. Learn how to set this attribute to specify is mandatory that the property have a value.
LastMod: 2023-02-25
Tags: code-first fluent-api data-annotations
---

# Entity Framework Required Data Annotation

The `Required` attribute can be applied to a property to make it a required property in an entity class. 

 - EF will create a `NOT NULL` column in a database table for a property on which the `Required` attribute is applied. 
 - If you save an entity without assigning a value to the `Required` property, then EF 6 will throw the `System.Data.Entity.Validation.DbEntityValidationException` exception.

```csharp
public class Book
{
    public int BookId { get; set; }
    [Required]
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/zn9grE)