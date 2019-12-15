# UseLogDump

## Description

When the `UseLogDump` property is `true`, the [LogDump](log-dump.md) property stores all messages of type "Information".

```csharp
/// <summary>Gets or sets the `UseLogDump` property. When the `UseLogDump` property is `true`, the [LogDump](log-dump.md) property stores all messages of type "Information".</summary>
public bool UseLogDump { get; set; }
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
connection.UseBulkOptions(options => 
{ 
	options.UseLogDump = true;
	options.LogDump = sb;
})
.BulkMerge(list); 

// Result
Console.WriteLine(sb.ToString());
```

Try it: [.NET Core](https://dotnetfiddle.net/j3x8uw) | [.NET Framework](https://dotnetfiddle.net/22kwZP)

### Result


We outputted the `StringBuilder` text which contains all SQL statements executed in the database.

