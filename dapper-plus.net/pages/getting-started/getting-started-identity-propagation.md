---
Name: Getting Started with Identity Propagation
LastMod: 2023-02-26
---

# Identity Propagation

## Description

Propagating the identity is often needed when an entity has some relationship without navigation property.

For example, the invoice has invoice items which also hold the `InvoiceID` value.

In Dapper Plus, there is 3 major ways to propagate the identity:

- Auto Identity Propagation
- AfterAction
- ThenForEach

## Auto Identity Propagation

The auto identity propagation is the easiest ways. You simply specify during the mapping that the identity value should be auto propagated (set the second parameter to `true`).

```csharp
context.Entity<Invoice>().Identity(x => x.InvoiceID, true);
```
[Try it](https://dotnetfiddle.net/LBfItU)

However, some restriction applies. The identity must have either one of the those following convention (case insensitive):

- `ID`, in this case, we will automatically propagate to all properties with the name `[EntityType]ID`, in our example `InvoiceID`.
- Or directly `[EntityType]ID`, in our example `InvoiceID`.

## AfterAction

The `AfterAction` method allows you during the mapping to specify a custom action to perform after an operation has been made.

```csharp
DapperPlusManager.Entity<Invoice>().Identity(x => x.InvoiceID)
	.AfterAction((actionKind, invoice) =>
		 {
			 if (actionKind == DapperPlusActionKind.Insert || actionKind == DapperPlusActionKind.Merge)
			 {
				 if (invoice.InvoiceMeta != null)
				 {
					 invoice.InvoiceMeta.InvoiceID = invoice.InvoiceID;
				 }

				 if (invoice.InvoiceItems != null)
				 {
					 invoice.InvoiceItems.ForEach(x => x.InvoiceID = invoice.InvoiceID);
				 }
			 }
		 });
```
[Try it](https://dotnetfiddle.net/yDPhxS)

## ThenForEach

The `ThenForEach` method allows you to make a custom action after a bulk-operation has been made.

```csharp
connection.BulkInsert(invoices)
		.ThenForEach(invoice => {
			if(invoice.InvoiceMeta != null)
			{
				invoice.InvoiceMeta.InvoiceID = invoice.InvoiceID;
			}
			  
			if(invoice.InvoiceItems != null)
			{
				invoice.InvoiceItems.ForEach(invoiceItem => invoiceItem.InvoiceID = invoice.InvoiceID);
			}
		  })
		.AlsoBulkInsert(x => x.InvoiceMeta)
		.ThenBulkInsert(x => x.InvoiceItems);
```
[Try it](https://dotnetfiddle.net/HhJ06l)
