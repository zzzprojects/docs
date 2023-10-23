---
PermaID: 1000190
Title: Dapper Contrib Table - Use Data Annotation to Specify Table Name
MetaDescription: Unlock the power of Dapper Contrib by using 'Table' Data Annotation. Learn how to map your entity to a destination table name.
LastMod: 2023-10-21
---

# Dapper Contrib Table: Use Data Annotation to Specify Table Name

## Description

The `Table` attribute can be used to decorate a data model class to specify the name of the database table that the class represents. It specifies the destination table name mapped to the entity.

 - By default, the pluralized name of the class is used as a table name. 
 - You can use another table name by specifying the `Table` attribute.

 In the following example, we are using the `Table` attribute to map the `InvoiceContrib` class to the `Invoice` table in the database. 
 
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
}
```