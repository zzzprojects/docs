---
PermaID: 1000159
Name: LINQ does not recognize method
---

# LINQ Does Not Recognize Method

## Exception: LINQ to Entities does not recognize the method 

It is the most common exception occurs when working with entity framework and converting data inside IQueryable result for filtering. 

```csharp
using (var context = new CustomerContext())
{
    var item = context.InvoiceItems
        .Where(i => i.Code == code.ToString())
        .FirstOrDefault();
}
```

### StackOverflow Related Questions

 - [Why LINQ to Entities does not recognize the method 'System.String ToString()?](https://stackoverflow.com/questions/10110266/why-linq-to-entities-does-not-recognize-the-method-system-string-tostring)
 - [LINQ to Entities does not recognize the method 'System.String Format(System.String, System.Object, System.Object)'](https://stackoverflow.com/questions/10079990/linq-to-entities-does-not-recognize-the-method-system-string-formatsystem-stri)
 - [LINQ to Entities does not recognize the method 'System.String ToString()' method, and this method cannot be translated into a store expression](https://stackoverflow.com/questions/5899683/linq-to-entities-does-not-recognize-the-method-system-string-tostring-method)

## Solution

There are different solutions for this specific problem.

### Move ToString() call to a separate line.

```csharp
using (var context = new CustomerContext())
{
    string codeStr = code.ToString();
    var item = context.InvoiceItems
        .Where(i => i.Code == codeStr)
        .FirstOrDefault();
}
```

### Use EF Extension Method,

```csharp
using (var context = new CustomerContext())
{
    var item = context.InvoiceItems
        .Where(i => i.Code == SqlFunctions.StringConvert(code))
        .FirstOrDefault();
}
```

### Convert IQueryable result to IEnumerable before Filtering

```csharp
using (var context = new CustomerContext())
{
    var item = context.InvoiceItems.AsEnumerable()
        .Where(i => i.Code == code.ToString())
        .FirstOrDefault();
}
```