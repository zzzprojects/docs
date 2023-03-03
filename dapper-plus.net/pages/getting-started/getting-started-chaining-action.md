---
Name: Getting Started with Chaining Action
LastMod: 2023-02-26
---

# Chaining Action

## Action

All Bulk Operations start with one of the following actions:
- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge)

You can include lists directly in the method or you can chain your action with `Also[Action]` and `Then[Action]` methods.

```csharp
connection.BulkInsert(invoices, x => x.InvoiceMeta, x => x.InvoiceItems, x=> x.InvoiceItems.Select(y => y.InvoiceItemMeta));
```

[Try it](https://dotnetfiddle.net/cmY12C)

## Also Action

When you chain your action with the `Also[Action]` method, you stay at the same level.

For example, if you `BulkInsert` an invoice, and use `AlsoBulkInsert` on your InvoiceItems, the level is still the Invoice.

So, the next Bulk Operations will be related to the Invoice, in this example, the InvoiceMeta.

```csharp
connection.BulkInsert(invoices)
		  .AlsoBulkInsert(invoice => invoice.InvoiceMeta)
		  .AlsoBulkInsert(invoice => invoice.InvoiceItems, invoiceItem => invoiceItem.InvoiceMeta);
```

[Try it](https://dotnetfiddle.net/EDpO3F)

Method:
- [AlsoBulkInsert](/also-bulk-insert)
- [AlsoBulkUpdate](/also-bulk-update)
- [AlsoBulkDelete](/also-bulk-delete)
- [AlsoBulkMerge](/also-bulk-merge)

## Then Action

When you chain your action with the `Then[Action]` method, you move to the next level

For example, if you `BulkInsert` an invoice, and use `ThenBulkInsert` on your InvoiceItems, the level is now the InvoiceItems.

So, the next Bulk Operations will be related to the InvoiceItem, in this example, the InvoiceItemMeta.

```csharp
connection.BulkInsert(invoices)
		  .AlsoBulkInsert(invoice => invoice.InvoiceMeta)
		  .ThenBulkInsert(invoice => invoice.InvoiceItems)
		  .ThenBulkInsert(invoiceItem => invoiceItem.InvoiceItemMeta);
```

[Try it](https://dotnetfiddle.net/AvlpRS)

Method:
- [ThenBulkInsert](/then-bulk-insert)
- [ThenBulkUpdate](/then-bulk-update)
- [ThenBulkDelete](/then-bulk-delete)
- [ThenBulkMerge](/then-bulk-merge)
