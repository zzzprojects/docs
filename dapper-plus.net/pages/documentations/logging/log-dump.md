# LogDump

## Description

The `LogDump` property stores all messages of type "Information". This option requires to set the `UseLogDump` property to `true`.

```csharp
/// <summary>Gets or sets the LogDump property. The `LogDump` property stores all messages of type "Information". This option requires to set the `UseLogDump` property to `true`.</summary>
public StringBuilder LogDump { get; set; }
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