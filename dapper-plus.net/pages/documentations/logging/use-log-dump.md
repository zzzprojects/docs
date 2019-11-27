# UseLogDump

## Description

When the `UseLogDump` property is `true`, the `LogDump` property stores all messages of type "Information".

```csharp
/// <summary>Gets or sets the UseLogDump property. When the `UseLogDump` property is `true`, the `LogDump` property stores all messages of type "Information".</summary>
public bool UseLogDump { get; set; }
```

## Example

We will demonstrate how to use the `LogDump` to log all messages.

### Execute

First, we will use the `UseBulkOptions` method to set:
- `UseLogDump = true`: To enable the `LogDump`.
- `LogDump = sb`: To specify the `StringBuilder` to use to dump all messages.

Then we will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

### Code

```csharp
[TBD]
```

Try it: [.NET Core]([TBD]) | [.NET Framework]([TBD])

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.