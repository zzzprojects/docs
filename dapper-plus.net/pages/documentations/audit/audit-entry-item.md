# AuditEntryItem

## Description

The `AuditEntryItem` is a property of the `AuditEntry` class.

It represent metadata about a specific column:
- ColumnName
- NewValue
- OldValue

```csharp
namespace Z.BulkOperations
{
    /// <summary>An audit entry item.</summary>
    public class AuditEntryItem
    {
        /// <summary>Gets or sets the name of the column.</summary>
        /// <value>The name of the column.</value>
        public string ColumnName { get; set; }

        /// <summary>Gets or sets the new value.</summary>
        /// <value>The new value.</value>
        public object NewValue { get; set; }

        /// <summary>Gets or sets the old value.</summary>
        /// <value>The old value.</value>
        public object OldValue { get; set; }
    }
}
```

> HINT: The `AuditEntryItem` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example,

We will execute a `BulkMerge` on a list that contains **1** new customer and **2** existing customers.

As a result, we will display all created `AuditEntry` and their `AuditEntryItem`.


```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)