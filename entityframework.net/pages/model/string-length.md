---
PermaID: 1000116
Title: Entity Framework StringLength Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the StringLength Data Annotations. Learn how to set this attribute to specify the length of a string, including the minimum and maximum length.
LastMod: 2025-06-17
Tags: code-first fluent-api data-annotations
---

# Entity Framework StringLength Data Annotation

The `StringLength` attribute allows you to specify additional property validations like `MaxLength`. 

 - It is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to.
 - The only difference is that `StringLength` attribute can only be applied to a string type property of Domain classes.

The following example specifies that the `Title` column in the Books table has a maximum length of 12 characters.

```csharp
public class Book
{
    public int BookId { get; set; }
    [StringLength(12)]
    public string Title { get; set; }
}
```
[Try it](https://dotnetfiddle.net/5DVfLR)

The StringLength attribute also allows you to specify a minimum acceptable length for a string.

```csharp
public class Book
{
    public int BookId { get; set; }
    [StringLength(12, MinimumLength = 5)]
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/0fhjXY)

The `MinimumLength` has no effect on database configuration, but you can use it to provide a client-side validation in validation-aware UI frameworks, such as ASP.NET MVC.