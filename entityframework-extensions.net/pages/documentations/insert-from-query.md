# Insert from Query

## Definition
`INSERT` all rows from the database using a LINQ Query without loading entities in the context.

An `INSERT` statement is built using the LINQ expression and directly executed in the database.

You can `INSERT` in any destination table (doesn't have to be part of your model).

```csharp
// INSERT all customers that are inactive for more than two years into a backup table
var date = DateTime.Now.AddYears(-2);
context.Customers
    .Where(x => x.IsActive && x.LastLogin < date)
    .InsertFromQuery("bck_Customer", x => new { x.CustomerID, x.Name, x.Email });
```

## Purpose
`Inserting` entities using `SaveChanges` normally requires loading them first in the `ChangeTracker` if you want to copy existing ones. These additional round-trips are often not necessary.

`InsertFromQuery` gives you access to directly execute an `INSERT` statement in the database and provide a **HUGE** performance improvement.

## Performance Comparisons

| Operations      | 1,000 Entities | 2,000 Entities | 5,000 Entities |
| :-------------- | -------------: | -------------: | -------------: |
| SaveChanges     | 1,000 ms       | 2,000 ms       | 5,000 ms       |
| InsertFromQuery | 1 ms           | 1 ms           | 1 ms           |

## Limitations

- DO NOT support EF Core 2.x due to client-side evaluation