---
Name: Log
LastMod: 2023-03-01
---

# Log

## Description

The `Log` property is an action executed when a message of type "Information" happens.

```csharp
/// <summary>Gets or sets the Log property. The `Log` property is an action executed when a message of type "Information" happens.</summary>
public Action<string> Log { get; set; }
```

## Example

We will demonstrate how to log all messages into a `StringBuilder` using the `Log` property.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer, and **2** existing customers.

We will use the following BulkOptions:
- `Log`: To specify the action to append the message in the `StringBuilder`.

### Code

```csharp
// Execute
var sb = new StringBuilder();
context.BulkMerge(list, options =>
{
    options.Log = s => sb.AppendLine(s);
});

// Result
Console.WriteLine(sb.ToString());
```

Try it: [.NET Core](https://dotnetfiddle.net/tnK9X0) | [.NET Framework](https://dotnetfiddle.net/U7Dsaf)

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.
