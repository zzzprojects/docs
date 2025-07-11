---
PermaID: 1000188
Title: Dapper Contrib ExplicitKey - Use Data Annotation For Key Column
MetaDescription: Unlock the power of Dapper Contrib by using 'ExplicitKey' Data Annotation. Learn how to specify which properties are a part of the not autoincremented primary keys.
LastMod: 2025-06-16
---

# Dapper Contrib ExplicitKey: Use Data Annotation For Key Column

## Description

The `Key` attribute assumes an autoincremented key, and when you try to pass a custom key instead, it will fail with `NullReferenceException`, even if the passed value isn't null. 

 - To solve this problem `ExplicitKey` was introduced to handle those instead. 
 - It Specifies the property as a key that is **NOT** automatically generated by the database.
 - The `ExplicitKey` attribute is used to identify multiple columns as the primary key. 
 - It is useful when you have a composite primary key (a primary key that consists of more than one column).

In the following example, the `InvoiceID` property of the `InvoiceDetailContrib` class is decorated with an `ExplicitKey` attribute to specify the primary key of a table.

```csharp
[Table("InvoiceDetail")]
public class InvoiceDetailContrib
{
	[ExplicitKey]
	public int InvoiceID { get; set; }

	public string Detail { get; set; }
}        

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoice = new InvoiceContrib {Kind = InvoiceKind.WebInvoice, Code = "Insert_Single_1"};
	connection.Insert(invoice);

	var invoiceDetail = new InvoiceDetailContrib {InvoiceID = invoice.InvoiceID, Detail = "Insert_Single_1"};
	connection.Insert(invoiceDetail);
}
```

The `InvoiceID` property of the `InvoiceDetailContrib` class must be assigned a value because it will not be generated automatically by the database.
