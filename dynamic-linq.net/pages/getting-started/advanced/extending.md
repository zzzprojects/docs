---
Permalink: advanced-extending
Name: Extending Dynamic LINQ
---

# Extending

There are several ways to extend the functionality from Dynamic LINQ library.

## DynamicLinqType - attribute

It's possible to extend the parsing functionality from Dynamic LINQ by defining some additional logic in static helper/utility classes. To be able to do this, there are several requirements:

- The class needs to be a public static class
- The methods in this class need also to be public and static
- The class itself needs to be annotated with the `[DynamicLinqType]` attribute

### Example

Create the following utility class.

``` csharp
[DynamicLinqType]
public static class Utils
{
    public static int ParseAsInt(string value)
    {
        if (value == null)
        {
             return 0;
        }

        return int.Parse(value);
    }
}
```

This class has simple logic to return a integer value of 0 when the input string is null, else parse the string to an integer.

### Usage

This can be used as follows:

``` csharp
var query = new [] { new { Value = (string) null }, new { Value = "100" } }.AsQueryable();
var result = query.Select("Utils.ParseAsInt(Value)");
```

[Try it online](https://dotnetfiddle.net/kkKdWi)

### Notes

This helper class + method will only work when using LINQ to Objects.
It will *not* work for LINQ to Entities and will throw an exception like
>System.NotSupportedException: LINQ to Entities does not recognize the method 'Int32 ParseAsInt(System.String)' method, and this method cannot be translated into a store expression.

See [failing example](https://dotnetfiddle.net/78MYH9).
