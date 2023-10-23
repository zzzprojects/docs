---
PermaID: 1000181
Title: Dapper Multi-Result Result - Learn How to Map With Multiple Results
MetaDescription: Unlock the power of Dapper by returning a Multi-Result Result by combining queries to map multiple entity types or dynamic objects. Learn how to query your database by combining your queries in a single one and using the resultset to read all your results.
LastMod: 2023-10-20
---

# Dapper Multi-Result Result: Discover How to Map With Multiple Results

## Description

The multi-result is the ability to map a single query to multiple objects. It is useful when you need to select data from multiple tables or when you need to select multiple columns from a single table. Dapper supports this by providing an extension method called [QueryMultiple](/querymultiple).

 - The `QueryMultiple` can be called from any object of type `IDbConnection`. 
 - It can execute multiple queries within the same command and map results.
 - It will return a resultset for each type you pass in and you can then use the `Read<T>` method on each resultset to get an `IEnumerable<T>` for that resultset.

 The following example shows how to execute two SQL statements in one round trip to the database. In the first query, we get all invoices from the `Invoices` table, and in the second query, we get all invoice items from the `InvoiceItems` table. 

```csharp
string sql = "SELECT * FROM Invoice WHERE InvoiceID = @InvoiceID; SELECT * FROM InvoiceItem WHERE InvoiceID = @InvoiceID;";

using (var connection = My.ConnectionFactory())
{
    connection.Open();

    using (var multi = connection.QueryMultiple(sql, new {InvoiceID = 1}))
    {
        var invoice = multi.Read<Invoice>().First();
        var invoiceItems = multi.Read<InvoiceItem>().ToList();
    }
}
```

In the above example, you can see that we have mapped the results of both queries to the `Invoice` and `InvoiceItem` classes.

## Related Articles

- [Dapper - QueryMultiple](/querymultiple)