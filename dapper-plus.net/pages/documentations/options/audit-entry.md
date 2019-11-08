# AuditEntry

## Description

The `AuditEntry` represent a row that has been modified.

It represent metadata about a specific row:
- Which tale affected...
- What action has been peformance
- When the action has been performed

```csharp
namespace Z.BulkOperations
{
    /// <summary>An audit entry.</summary>
    public class AuditEntry
    {
        /// <summary>Gets or sets the action.</summary>
        /// <value>The action.</value>
        public AuditActionType Action { get; set; }
		
        /// <summary>Gets or sets the Date/Time of the date.</summary>
        /// <value>The date.</value>
        public DateTime Date { get; set; }

        /// <summary>Gets or sets the metas.</summary>
        /// <value>The metas.</value>
        public Dictionary<object, object> Metas { get; set; }

        /// <summary>Gets or sets the name of the table.</summary>
        /// <value>The name of the table.</value>
        public string TableName { get; set; }

        /// <summary>Gets or sets the values.</summary>
        /// <value>The values.</value>
        public List<AuditEntryItem> Values { get; set; }
    }
}
```

> HINT: The `AuditEntry` is in Z.BulkOperations namespace since the library is used under the hood.

## Example

In this example,

A BulkMerge will be performed and the code will display all `AuditEntry` informations:
- 1 rows has been inserted
- 2 rows has been updated

```csharp
// ...code...
```

Try it: [.NET Framework](https://dotnetfiddle.net/XB5npF) | [.NET Core](https://dotnetfiddle.net/y4w1ZG)
