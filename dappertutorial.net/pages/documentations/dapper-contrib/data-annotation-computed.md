---
PermaID: 1000187
Title: Dapper Contrib Computed - Use Data Annotation With Computed Column
MetaDescription: Unlock the power of Dapper Contrib by using 'Computed' Data Annotation. Learn how to specify a property based on other property values.
LastMod: 2023-10-21
---

# Dapper Contrib Computed: Use Data Annotation With Computed Column

## Description

The `Computed` attribute specifies that the property should be excluded from the update. 

 - You can use `Computed` columns in the database and decorate your entity with the `Computed` attribute to prevent updating its value to the table.
 - The `Computed` attribute allows you to specify that a property should be computed based on the values of other properties. 

The following example shows how to use the `Computed` attribute on a property in which you don't want to update its value in the database table.

```csharp
[Table("Invoice")]
public class InvoiceContrib
{
	[Key]
	public int InvoiceID { get; set; }

	public string Code { get; set; }
	public InvoiceKind Kind { get; set; }

	[Write(false)]
	[Computed]
	public string FakeProperty { get; set; }
}

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.GetAll<InvoiceContrib>().ToList();

	// The FakeProperty is skipped
	invoices.ForEach(x => x.FakeProperty += "z");

	var isSuccess = connection.Update(invoices);
}
```