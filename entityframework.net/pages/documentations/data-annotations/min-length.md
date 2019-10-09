---
PermaID: 1000119
Name: Min Length
---

# MinLength

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