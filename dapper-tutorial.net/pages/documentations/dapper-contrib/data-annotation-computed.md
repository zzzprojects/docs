---
PermaID: 1000187
Name: Data Annotation Computed
---

# Dapper Contrib - Data Annotation - Computed

## Description
Specifie the property should be excluded from update.

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