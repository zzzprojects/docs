---
title: [SOLVED] - Fixing 'The connection had been disposed.'
description: Learn how to fix the 'The connection had been disposed.' error in Dapper. Understand how using async method and non-buffered options can cause this.
canonical: /the-connection-had-been-disposed
status: Published
lastmod: 2024-02-23
---

# The connection had been disposed.

## Exception Message

You received the following error message: `The connection had been disposed.`

<div class="image-outer"><img src="https://www.learndapper.com/images/dapper/troubleshooting/the-connection-had-been-disposed/the-connection-had-been-disposed.png" loading="lazy" alt="Exception - The connection had been disposed."></div>

Stack Trace:

```txt
This exception was originally thrown at this call stack:
    MySql.Data.MySqlClient.MySqlConnection.Throw(System.Exception)
    MySql.Data.MySqlClient.MySqlConnection.OpenAsync(bool, System.Threading.CancellationToken)
    Dapper.SqlMapper.QueryAsync<T>(System.Data.IDbConnection, System.Type, Dapper.CommandDefinition) in SqlMapper.Async.cs
    Z.Dapper.Plus.Lab.Troubleshooting.ConnectionHasBeenDisposed.ConnnectionHasBeenDisposedExample.AnonymousMethod__0() in Request_ConnectionDisposed.cs
    Z.Dapper.Plus.Lab.Troubleshooting.ConnectionHasBeenDisposed.ConnnectionHasBeenDisposedExample() in Request_ConnectionDisposed.cs
```

## Async Issue

### Async Cause

Dapper does not dispose of the connection; it might open and close it but will not dispose of it. Therefore, the issue is likely in your code.

With developers using asynchronous methods everywhere today, the number one cause is probably a misuse of the async method.

Here is a bad async usage example that can randomly throw the error:

```csharp
using (var connection = new MySqlConnection(My.Connection))
{
	connection.QueryAsync<Customer>("SELECT * FROM Customer")
}
```

In this example, the [QueryAsync](/dapper-query/selecting-multiple-rows#dapper-queryasync) method might sometimes throw the error if it's executed after we leave the `using` block. Remember, if you do not await a task, the task can be executed at any time.

Here is a method that will throw the error 100% of the time:

```csharp
public static async Task ConnnectionHasBeenDisposedExample()
{
	Task task;

	using (var connection = new MySqlConnection(My.Connection))
	{
		task = Task.Run(async () =>
		{
			await Task.Delay(1000);
			await connection.QueryAsync<Customer>("SELECT * FROM Customer");

		});
	}

	await task;
}
```

The method has been tested with [MySql.Data v8.3](https://www.nuget.org/packages/MySql.Data/8.3.0)

As you can see in this example, we run the code in a task to be able to add a delay of 1 second. With this delay, we ensure that we leave the `using` block before the [QueryAsync](/dapper-query/selecting-multiple-rows#dapper-queryasync) method is executed.

### Async Solution

To solve the `The connection had been disposed.` issue when an async method is wrongly used, you simply have to ensure you are using the `async` method properly by using the `await` keyword.

```csharp
using (var connection = new MySqlConnection(My.Connection))
{
	await connection.QueryAsync<Customer>("SELECT * FROM Customer") // .ConfigureAwait(false) if needed
}
```

You can also use the `.ConfigureAwait(false)` method if needed to prevent the continuation from capturing the current synchronization context. This can be beneficial in scenarios where you want to avoid deadlock situations by allowing the continuation to run on a different context.

## Non-buffered Query Issue

### Non-buffered Query Cause

A second cause reported by [mgravell](https://github.com/DapperLib/Dapper/issues/2036#issuecomment-1959980305) of this issue could happen when you are using a non-buffered query and the connection is disposed while you are iterating over the results.

Let's take the following code as an example:

```csharp
public static void ConnectionDisposed_WithBufferedFalse()
{
	foreach (var customer in GetCustomers())
	{
		// ...code...
	}
}

public static IEnumerable<Customer> GetCustomers()
{
	using var conn = new MySqlConnection(My.Connection);
	return conn.Query<Customer>("SELECT * FROM Customer", buffered: false);
}
```

Because we used the option `buffered: false`, it means that the result will not be in the memory but instead streamed. In other words, the result will be returned on demand while we are iterating over the results.

However, in the example, the connection has been created in the `GetCustomers()` methods. So when the method returns the `IEnumerable<Customer>`, the connection has already been disposed, which leads to our `The connection had been disposed.` error.

The same issue can happen with any Dapper method using the option `buffered: false` and the [QueryUnbufferedAsync](/dapper-query/selecting-unbuffered-async) method:

```csharp
public static async Task ConnectionDisposed_WithQueryUnbufferedAsync()
{
	await foreach (var customer in GetCustomers())
	{
		// ...code...
	}
}

public static IAsyncEnumerable<Customer> GetCustomers()
{
	using var connection = new MySqlConnection(My.Connection);

	return connection.QueryUnbufferedAsync<Customer>("SELECT * FROM Customer");
}
```

### Non-buffered Query Solution

To fix this issue, you need to ensure the connection remains alive while you are iterating.

For the [Query Method](/dapper-query/selecting-multiple-results):

```csharp
public static void ConnectionDisposed_WithBufferedFalse()
{
	using var connection = new MySqlConnection(My.Connection);

	foreach (var customer in GetCustomers(connection))
	{
		// ...code...
	}
}

public static IEnumerable<Customer> GetCustomers(MySqlConnection connection)
{
	return connection.Query<Customer>("SELECT * FROM Customer", buffered: false);
}
```

For the [QueryUnbufferedAsync Method](/dapper-query/selecting-unbuffered-async):

```csharp
public static async Task ConnectionDisposed_WithQueryUnbufferedAsync()
{
	using var connection = new MySqlConnection(My.Connection);

	await foreach (var customer in GetCustomers(connection))
	{
		// ...code...
	}
}

public static IAsyncEnumerable<Customer> GetCustomers(MySqlConnection connection)
{
	return connection.QueryUnbufferedAsync<Customer>("SELECT * FROM Customer");
}
```

## Conclusion

Scenarios that throw the exception `The connection had been disposed.` are not related to an issue with Dapper but rather a bad usage of the connection. You can easily reproduce all those errors without Dapper.

It's important to note that different providers or different versions within a provider will throw different errors or have different behavior. However, the solution will always be the same if the cause was a wrong usage of the connection. For example:
- SQL Server: will throw instead the exception message `The ConnectionString property has not been initialized.`
- MySQL: Started to support correctly async method starting from v8.0.33. Before this version, all queries [ran synchronously](https://bugs.mysql.com/bug.php?id=70111). So like [bgrainger commented](https://github.com/DapperLib/Dapper/issues/2036#issuecomment-1960029026), some code that was "working" correctly with previous version could start to break with more latest version.

## Related Articles

- [Query](/dapper-query/selecting-multiple-results)
- [Query Async](/dapper-query/selecting-multiple-rows#dapper-queryasync)
- [Query Unbuffered Async](/dapper-query/selecting-unbuffered-async)