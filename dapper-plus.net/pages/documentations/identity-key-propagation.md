---
Title: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
MetaDescription: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
LastMod: 2024-06-27
---

# Master Easy Techniques to Propagate Identity and Key Values Across Your Graph

In the previous article, we learned about [supported datasource](#)

Propagating the identity is often needed when an entity has some relationship without navigation property.

For example, the invoice has invoice items which also hold the `InvoiceID` value.

In Dapper Plus, there is 4 major ways to propagate the identity:

- Auto Identity Propagation
- BeforeAction
- AfterAction
- ThenForEach


## Auto Identity Propagation 

**NAMING COnventions...**

The auto identity propagation is the easiest ways. You simply specify during the mapping that the identity value should be auto propagated (set the second parameter to `true`).

```csharp
context.Entity<Invoice>().Identity(x => x.InvoiceID, true);
```
[Try it](https://dotnetfiddle.net/LBfItU)

However, some restriction applies. The identity must have either one of the those following convention (case insensitive):

- `ID`, in this case, we will automatically propagate to all properties with the name `[EntityType]ID`, in our example `InvoiceID`.
- Or directly `[EntityType]ID`, in our example `InvoiceID`.

## BeforeAction Event

The `BeforeAction` event happen before an operation is performed. This event is important to be able to set some value such as `CreatedDate` and `UpdatedDate` but also ensure the key value from the parent entity is set.

In the following example, we will set our audit value properties but also check that the `InvoiceID` has been set. If that's not the case, we will get it first from the parent.

```csharp

```

## AfterAction Event 

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

## ThenForEach Method

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

## Conclusion

In this article, we learned how the identity value can be automatically propagated by following a naming convention but also how to manually propagate the key before or after a bulk operations has been performed.

We now completed our getting started section about how to customize [Bulk Extensions](#) method and [Single Extensions](#) methods.

It's time to see some more method that could help you through to [Improve your Dapper Experience](#)