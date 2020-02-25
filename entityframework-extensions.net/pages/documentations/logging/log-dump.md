# LogDump

## Description

The `LogDump` property stores all messages of type "Information".

This option requires to set the [UseLogDump](use-log-dump.md) property to `true`.

```csharp
/// <summary>Gets or sets the `LogDump` property. The `LogDump` property stores all messages of type "Information". This option requires to set the [UseLogDump](use-log-dump.md) property to `true`.</summary>
public StringBuilder LogDump { get; set; }
```

## Example

We will demonstrate how to use the `UseLogDump` and `LogDump` properties to log all messages.

### Execute

We will execute a `BulkMerge` on a list that contains **1** new customer, and **2** existing customers.

We will use the following BulkOptions:
- `UseLogDump`: To enable the `LogDump`.
- `LogDump`: To specify the `StringBuilder` to use to dump all messages.

### Code

```csharp
// Execute
var sb = new StringBuilder();
context.BulkMerge(list, options =>
{
    options.UseLogDump = true;
    options.LogDump = sb;
    
});

// Result
Console.WriteLine(sb.ToString());
```

Try it: [.NET Core](https://dotnetfiddle.net/4MdLDi) | [.NET Framework](https://dotnetfiddle.net/5JaqsJ)

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.
