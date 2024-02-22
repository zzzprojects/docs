---
title: [SOLVED] - Fixing 'The connection had been disposed.'
description: Learn how to fix the 'The connection had been disposed.' error in Dapper. Understand how using async method can cause this.
canonical: /the-connection-had-been-disposed
status: Published
lastmod: 2024-02-22
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

## Cause

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

## Solution

To solve the `The connection had been disposed.` issue when an async method is wrongly used, you simply have to ensure you are using the `async` method properly by using the `await` keyword.

```csharp
using (var connection = new MySqlConnection(My.Connection))
{
	await connection.QueryAsync<Customer>("SELECT * FROM Customer") // .ConfigureAwait(false) if needed
}
```

You can also use the `.ConfigureAwait(false)` method if needed to prevent the continuation from capturing the current synchronization context. This can be beneficial in scenarios where you want to avoid deadlock situations by allowing the continuation to run on a different context.

## Note

It's important to note that different providers or different versions within a provider will throw different errors. However, the solution will always be the same if the cause was a wrong usage of the `Async` method.

## Related Articles

- [Query Async](/dapper-query/selecting-multiple-rows#dapper-queryasync)
