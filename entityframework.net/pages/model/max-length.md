---
PermaID: 1000120
Name: Max Length
---

# MaxLength

The `MaxLength` attributes allow you to specify additional property validations. It is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to.

The following example specifies that the `Title` column in the Books table have a maximum length of 12 characters.

```csharp
public class Book
{
    public int BookId { get; set; }
    [MaxLength(12)]
    public string Title { get; set; }
}
```
[Try it](https://dotnetfiddle.net/Zj2xjQ)

If you set a value of Title property greater than the specified length in `MaxLength` attribute, EF will throw an `EntityValidationError`.

> Validation failed for one or more entities. See 'EntityValidationErrors' property for more details.

[Try it](https://dotnetfiddle.net/cU0j7q)

`MaxLength` attribute can also be used with `MinLength` attribute.

```csharp
public class Book
{
    public int BookId { get; set; }
    [MaxLength(12), MinLength(5)]
    public string Title { get; set; }
}
```

[Try it](https://dotnetfiddle.net/WEeLzB)