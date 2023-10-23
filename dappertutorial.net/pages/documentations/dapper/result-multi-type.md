---
PermaID: 1000182
Title: Dapper Multi-Type Result - Learn How to Map Result to Different Types
MetaDescription: Unlock the power of Dapper by returning a Multi-Type Result to map multiple types from an inheritance. Learn how to query your database and map your inheritance such as `TPC`, `TPT`, and `TPH`.
LastMod: 2023-10-20
---

# Dapper Multi-Type Result: Discover How to Map Result to Different Types

## Description

One of the features of Dapper is its ability to map columns from a query result to properties of a different type and you can call it a "multi-type" result. 

 - You can use the `ExecuteReader` method which can be called from any object of type `IDbConnection`. 
 - It can execute a query and map the result to a list of different types.
 - It is useful when you want to combine the data from multiple rows into a single object. 
 - For example, if you have a query that returns a list of store invoices and web invoices, you can use a multi-type result feature to map each kind of invoice to the `Invoice` object.

The following example shows how to use the `ExecuteReader` method and get the multi-type result.

```csharp
string sql = "SELECT * FROM Invoice;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    var invoices = new List<Invoice>();

    using (var reader = connection.ExecuteReader(sql))
    {
        var storeInvoiceParser = reader.GetRowParser<StoreInvoice>();
        var webInvoiceParser = reader.GetRowParser<WebInvoice>();

        while (reader.Read())
        {
            Invoice invoice;

            switch ((InvoiceKind) reader.GetInt32(reader.GetOrdinal("Kind")))
            {
                case InvoiceKind.StoreInvoice:
                    invoice = storeInvoiceParser(reader);
                    break;
                case InvoiceKind.WebInvoice:
                    invoice = webInvoiceParser(reader);
                    break;
                default:
                    throw new Exception(ExceptionMessage.GeneralException);
            }

            invoices.Add(invoice);
        }
    }
}
```

In the above example, both store invoices and web invoices are mapped to the `List<Invoice>`.