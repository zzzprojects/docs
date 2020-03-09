# Basic Usage

## Download

First, you need to download the library on [NuGet](https://www.nuget.org/packages/Z.Dapper.Plus/).

Dapper Plus support:

- .NET Core 2.0 or greater
- .NET Framework 4.0 or greater

## Extension Methods

When the library is added to your projects, the following extension methods are now automatically available from your `IDbConnection` and `IDbTransaction` interface:

- [BulkInsert](/bulk-insert)
- [BulkUpdate](/bulk-update)
- [BulkDelete](/bulk-delete)
- [BulkMerge](/bulk-merge)
- BulkSynchronize
- And more

## Connection

To perform a Bulk Operation on your IDbConnection:

1. Create your connection
2. Call the bulk operation from the connection

```csharp
// 1. Create your connection
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

// 2. Call the bulk operation from the connection
connection.BulkInsert(invoices);
```

[Try it](https://dotnetfiddle.net/joeOjo)

## Transaction

To perform a Bulk Operations on your IDbTransaction

1. Create your connection
2. Create your transaction from the connection
3. Call the bulk operation from the transaction
4. Commit the transaction

```csharp
// 1. Create your connection
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

// 2. Create your transaction from the connection
connection.Open();
var transaction = connection.BeginTransaction();

// 3. Call the bulk operation from the transaction
transaction.BulkInsert(invoices);

// 4. Commit the transaction
transaction.Commit();
```

[Try it](https://dotnetfiddle.net/vytN0w)

## Online Example

Whenever you want to check how an option works, make sure to search it in our [Online Examples](/online-examples) section.

Hundreds of examples are available to get you started!
