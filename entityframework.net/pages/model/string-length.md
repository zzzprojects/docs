---
PermaID: 1000116
Name: String Length
---

# StringLength

The `StringLength` attributes allow you to specify additional property validations like `MaxLength`. 

 - It is applied to a property to specify a maximum number of characters or bytes for the column that the property should map to.
 - The only difference is that `StringLength` attribute can only be applied to a string type property of Domain classes.

The following example specifies that the `Title` column in the Books table have a maximum length of 12 characters.

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