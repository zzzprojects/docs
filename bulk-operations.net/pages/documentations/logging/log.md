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
using (var bulk = new BulkOperation(connection))
{
    bulk.DestinationTableName = "Customer";
   
    bulk.Log = s => sb.AppendLine(s);     
    bulk.BulkMerge(list);
}

Console.WriteLine(sb.ToString());
```

Try it: [.NET Core](https://dotnetfiddle.net/DWR7q3) | [.NET Framework](https://dotnetfiddle.net/mOBlhf)

### Result

We outputted the `StringBuilder` text which contains all SQL statements executed in the database.
