# Log

## Description

The `Log` property is an action executed when a message of type "Information" happens.

```csharp
/// <summary>Gets or sets the Log property. The `Log` property is an action executed when a message of type "Information" happens.</summary>
public Action<string> Log { get; set; }
```

## Example

We will demonstrate how to log all messages into a `StringBuilder`.

### Execute

We will set options in the `UseBulkOptions` methods:
- `Log = (s) => sb.AppendLine(s)`: to specify to append the message in the `StringBuilder`.

Then we will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.


### Code

```csharp
[TBD]
```

Try it: [.NET Core]([TBD]) | [.NET Framework]([TBD])

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.