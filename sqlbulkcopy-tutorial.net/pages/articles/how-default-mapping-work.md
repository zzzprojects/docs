---
Name: How the default mapping work
---

# SqlBulkCopy - How the default mapping work

## Description
Hey SqlBulkCopy can map column automaticality, it's not is great?

At first sight yes, but once you understand how it works, you will always map all your columns explicitly!

## Under the hood

### Unit of Test
Let use,

The following DataTable to map
```csharp
var dt = new DataTable();
dt.Columns.Add("SourceOrderId", typeof(int));
dt.Columns.Add("SourceTransactionId", typeof(int));
dt.Columns.Add("SourceCustomerId", typeof(int));
```

to the following SQL table:
{% highlight sql %}
CREATE TABLE DestinationOrder
(
    DestinationOrderId INT IDENTITY(1, 1),
    DestinationTransactionId INT,
    DestinationCustomerId INT,
    DestinationDateCreated DATETIME2,
    DestinationInvoiceId INT
)
```

### Problem

Let say we want to insert only the following column:
- SourceOrderId
- SourceTransactionId
- SourceCustomerId

#### Step 1
The first step of the default mapping is simply performing an auto-mapping by ordinal:

```csharp
internal void CreateDefaultMapping(int columnCount)
{
  for (int index = 0; index < columnCount; ++index)
    this.InnerList.Add((object) new SqlBulkCopyColumnMapping(index, index));
}
```

The maping become:

| Source              | Destination              |
| ------------------- | ------------------------ |
| SourceTransactionId | DestinationOrderId       |
| SourceCustomerId    | DestinationTransactionId |
| SourceReference     | DestinationCustomerId    |

Problem is without a doubt coming! Wrong DataTable column are mapped to wrong destination column.

#### Step 2
The second step will check destination column type and identity column.

The default mapping will discover the DestinationOrderId is an identity column and should not be mapped unless you have the option KeepIdentity turned on.

The default mapping will try to map the SourceTransactionId to the next column available.

- It will try to map to DestinationDateCreated but the type doesn't match
- It will try to map to DestinationInvoiceId and will find a perfect match!

The mapping is now:

| Source              | Destination            |
| ------------------- | ---------------------- |
| SourceTransactionId | DestinationInvoiceId   |
| SourceCustomerId    | DestinationReference   |
| SourceReference     | DestinationCustomerId  |

Without a doubt, this mapping will lead to many error. The most common error is a FOREIGN KEY.

## StackOverflow Reference
- [SqlBulkCopy giving FOREIGN KEY constraint error](http://stackoverflow.com/questions/39684342/sqlbulkcopy-giving-foreign-key-constraint-error/)