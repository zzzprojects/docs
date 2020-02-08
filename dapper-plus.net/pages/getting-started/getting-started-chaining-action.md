# Chaining Action

## Action

All Bulk Operations start with one of the following actions:
- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge)

After, you can chain your action with `Also[Action]` and `Then[Action]` methods.

```csharp
Connection.BulkInsert(Invoices)
```

## Also Action

When you chain your action with the `Also[Action]` method, you stay at the same level.

For example, if you `BulkInsert` an invoice, and use `AlsoBulkInsert` on your InvoiceItems, the level is still the Invoice.

So, the next Bulk Operations will be related to the Invoice, in this example, the InvoiceMeta.

```csharp
Connection.BulkInsert(Invoices).AlsoBulkInsert(x => x. InvoiceItems).AlsoBulkInsert(x => x.InvoiceMeta)
```

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
Connection.BulkInsert(Invoices).ThenBulkInsert(x => x.InvoiceItems).ThenBulkInsert(x => x.InvoiceItemMeta) + A third level
```

Method:
- [ThenBulkInsert](/then-bulk-insert)
- [ThenBulkUpdate](/then-bulk-update)
- [ThenBulkDelete](/then-bulk-delete)
- [ThenBulkMerge](/then-bulk-merge)
