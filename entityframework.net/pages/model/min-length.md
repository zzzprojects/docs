---
PermaID: 1000119
Title: Entity Framework MinLength Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the MinLength Data Annotations. Learn how to set this attribute to specify the minimum length of a string property.
LastMod: 2023-02-25
Tags: code-first fluent-api data-annotations
---

# Entity Framework MinLength Data Annotation

The `MinLength` attributes allow you to specify additional property validations. It is applied to a property to specify a minimum number of characters or bytes for the column that the property should map to.

The following example specifies that the `Title` column in the Books table must have a minimum length of 5 characters.

```csharp
public class Book
{
    public int BookId { get; set; }
    [MinLength(5)]
    public string Title { get; set; }
}
```
[Try it](https://dotnetfiddle.net/9nLeUA)

If you set a value of Title property less than the specified length in `MinLength` attribute, EF will throw an `EntityValidationError`.

> Validation failed for one or more entities. See 'EntityValidationErrors' property for more details.

[Try it](https://dotnetfiddle.net/gCdNJD)