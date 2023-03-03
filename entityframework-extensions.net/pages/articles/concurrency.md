---
Name: Concurrency EF Core
LastMod: 2023-02-25
---

# Concurrency (EF Core)

## Problem

Your model has a concurrency property, and you must resolve optimistic concurrency using a pattern.

Concurrency exceptions happens on:
- BulkSaveChanges
- BulkUpdate
- BulkDelete
- BulkMerge

## BulkSaveChanges

When a concurrency error happens, only the first entry in error is returned (exactly like SaveChanges).

There are three possible scenarios:
- Database Wins (You keep database values)
- Custom Resolution (You merge properties from database and client entity)
- Client Wins (You keep current entity values)

### Database Wins && Custom Resolution

If you want to keep database values or use a custom resolution (choose if you keep the current value or database value depending on the property) use a solution similar to this one:

```csharp
var saved = false;

while (!saved)
{
	try
	{
		// Attempt to save changes to the database
		context.BulkSaveChanges();
		saved = true;
	}
	catch (DbBulkOperationConcurrencyException ex)
	{
		foreach (var t in ex.Entries)
		{
			var entry = context.Entry(t);

			if (entry.Entity is EntitySimple)
			{
				var proposedValues = entry.CurrentValues;
				var databaseValues = entry.GetDatabaseValues();

				foreach (var property in proposedValues.Properties)
				{
					var proposedValue = proposedValues[property];
					var databaseValue = databaseValues[property];

					// CHOOSE  which value should be written to database
					proposedValues[property] = databaseValue;
				}

				// Refresh original values to bypass next concurrency check
				entry.OriginalValues.SetValues(proposedValues);
			}
		}
	}
}
```

For this solution ensures that your concurrency property value is updated with the database value.

### Client Wins

If you wish to keep the current value, there is two choice:

- Only update the concurrency property with the database value (See Database Wins && Custom Resolution solution)
- Use the `AllowConcurrency = false` option. When enabled, the `BulkSaveChanges` will no longer check if there is some concurrency error.


```csharp
context.BulkSaveChanges(options => options.AllowConcurrency = false);
```


## BulkUpdate / BulkDelete / BulkMerge

Due to backward compatibility, in EF Core, the concurrency is not enabled by default for those operations.

To enable it, you need to use the `EnableConcurrencyForBulkOperation = true` option:

```csharp
context.BulkUpdate(list, options =>
	{
		options.EnableConcurrencyForBulkOperation = true;
	});
```

When a concurrency error happens, the method will return a `DbBulkOperationConcurrencyException`, which contains all error entries.

Again there are three possible scenarios:

- Database Wins (You keep database values)
- Custom Resolution (You merge properties from database and client entity)
- Client Wins (You keep current entity values)

You can use the same logic as `BulkSaveChanges` to handle concurrency issues, depending on how you want to handle entities in error.