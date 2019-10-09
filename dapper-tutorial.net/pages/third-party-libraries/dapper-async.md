---
PermaID: 1000203
Name: Async
---

# Dapper-Async

## Overview
Dapper-Async is a small library package which extends the IDbConnection interface with Async (asynchronous) methods. With asynchronous programming, you can enhance the overall responsiveness of your application and also avoid performance bottlenecks. 

To allow asynchronous database queries, dapper has also added a set of QueryAsync methods using .NET Framework 4.5's Task-Based Asynchronous Pattern.

## Installation
Dapper-Async is only available through NuGet: <a href="https://www.nuget.org/packages/Dapper-Async/" target="_blank">https://www.nuget.org/packages/Dapper-Async/</a>

You can easily install this library by running the following command:

```csharp
PM> Install-Package Dapper-Async 
```

More information can be found at: <a href="https://github.com/StackExchange/Dapper" target="_blank">https://github.com/StackExchange/Dapper</a>

## APIs
Once you installed this library then it will extend your IDbConnection interface with the following methods:

- ExecuteAsync
- QueryAsync
- QueryFirstAsync
- QueryFirstOrDefaultAsync
- QuerySingleAsync
- QuerySingleOrDefaultAsync
- QueryMultipleAsync

You can use these extension methods easily in your code.

```csharp
var affectedRows = connection.ExecuteAsync(sql,
        new {Kind = InvoiceKind.WebInvoice, Code = "Single_Insert_1"},
        commandType: CommandType.StoredProcedure)
    .Result;
    
var invoices = connection.QueryAsync<Invoice>(sql).Result.ToList();

var invoice = connection.QueryFirstAsync<Invoice>(sql, new {InvoiceID = 1}).Result;

var invoice = connection.QueryFirstOrDefaultAsync<Invoice>(sql, new {InvoiceID = 1}).Result;

var invoice = connection.QuerySingleAsync<Invoice>(sql, new {InvoiceID = 1}).Result;

var invoice = connection.QuerySingleOrDefaultAsync<Invoice>(sql, new {InvoiceID = 1}).Result;

using (var multi = connection.QueryMultipleAsync(sql, new { InvoiceID = 1 }).Result)
{
    var invoice = multi.Read<Invoice>().First();
    var invoiceItems = multi.Read<InvoiceItem>().ToList();
}
```

Unfortunately, there is no proper documentation available for this library, but you can find examples here: <a href="http://dapper-tutorial.net/async" target="_blank">http://dapper-tutorial.net/async</a>

## Support
This library is supported on a regular basis and you will get your answers within next few days. <a href="https://github.com/StackExchange/Dapper/issues">https://github.com/StackExchange/Dapper/issues</a>