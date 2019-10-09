---
PermaID: 1000182
Name: Result Multi Type
---

# Dapper - Result Multi-Type 

## Description
ExecuteReader method is an extension method which can be called from any object of type IDbConnection. It can execute a query and map the result to a list of different types.

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