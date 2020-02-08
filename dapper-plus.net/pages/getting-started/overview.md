# Getting Started

## What is Dapper Plus?

Dapper Plus is a <a href="https://www.nuget.org/packages/Z.Dapper.Plus/">NuGet library</a> that will extend your `IDbConnection` and `IDbTransaction` interface with high-performance bulk operations:
- BulkInsert
- BulkUpdate
- BulkDelete
- BulkMerge
- BulkSynchronize
- And more

**.NET Core** and **.NET Framework** are supported

### Example

```csharp
DapperPlusManager.Entity<Invoice>().Identity(x => x.InvoiceID)
		
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());
		
// Easy to use
connection.BulkInsert(invoices);

// Easy to customize
connection.UseBulkOptions(options => options.InsertIfNotExists = true)
          .BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/nI4D4E)


## Who is it for?

If you need to make massive importation, have a multi-editing grid, need to synchronize data or just want an easy to use solution to save your entities than Dapper Plus is for you.

Easy to use, easy to customize!

## Why should you use it?

Querying your data is very easy with Dapper.

However, how do you save your entities? You need to either use the execute method or a third-party library like **Dapper Plus** for an easy to use solution.

Saving your data as fast as possible is very important to make your application **responsive**, and that's exactly what our library offer.

Also, we offer **outstanding customer** support and hundred of [Online Examples](/online-examples) to help you to get started.

## Database Provider Supported

- SQL Server 2008+
- SQL Azure
- SQL Compact
- Oracle
- MySQL
- PostgreSQL
- SQLite