---
PermaID: 1000191
Title: Dapper Contrib Write - Use Data Annotation to Specify Readonly Column
MetaDescription: Unlock the power of Dapper Contrib by using 'Write' Data Annotation. Learn how to specify whether a property should be writable in the database.
LastMod: 2023-10-21
---

# Dapper Contrib Write: Use Data Annotation to Specify Readonly Column

## Description

The `Write` attribute specifies whether a field is writable or not in the database. 

 - It must be applied to any properties that we do not want a column in a database table. 
 - For example, you might have a property in your entity class, but it does not need to be stored. 
 - You can apply the `Write` attribute to a property that you do not want to create a column of in the database.

The following example shows how to use the `Write` attribute on a property in which you don't want to store its value in the database table.

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

	var invoice = connection.Get<InvoiceContrib>(1);

	// The FakeProperty is null
}
```