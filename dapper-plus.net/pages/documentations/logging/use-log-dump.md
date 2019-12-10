# UseLogDump

## Description

The `BulkOperation.UseLogDump` property sets if all log related to database events should be stored in a `LogDump` property or not.

```csharp
/// <summary>Gets or sets a value indicating whether this object use log dump.</summary>
/// <value>true if use log dump, false if not.</value>
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
```

Try it: [.NET Core](https://dotnetfiddle.net/j3x8uw) | [.NET Framework](https://dotnetfiddle.net/22kwZP)

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.
