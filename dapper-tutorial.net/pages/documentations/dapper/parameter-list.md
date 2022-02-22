---
PermaID: 1000171
Name: List Parameter
---

# Dapper - List Parameter

## Description
Dapper allow you to specify multiple parameter on a IN clause by using a list.

```csharp
var sql = "SELECT * FROM Invoice WHERE Kind IN @Kind;";

using (var connection = My.ConnectionFactory())
{
	connection.Open();

	var invoices = connection.Query<Invoice>(sql, new {Kind = new[] {InvoiceKind.StoreInvoice, InvoiceKind.WebInvoice}}).ToList();
}

```