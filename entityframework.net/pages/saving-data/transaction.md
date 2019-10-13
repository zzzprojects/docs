---
PermaID: 1000039
Name: Transaction
---

# Transaction

In Entity Framework, when you call SaveChanges() to insert, delete, or update data to the database, the entity framework will wrap that operation in a transaction. 

 - The transaction allows several database operations to be processed atomically.
 - If it is committed, all of the operations are successfully applied to the database.
 - If the transaction is rolled back, none of the operations are applied to the database.

## Default Behaviour

 - In Entity Framework, `SaveChanges` automatically starts a transaction and commits or rolls it back.
 - It means the Entity Framework maintains a transaction for the multiple entity inserts, update and delete in a single `SaveChanges()` method. 
 - When we execute another operation, the Entity Framework creates a new transaction.

The following example saves two author entities by calling `SaveChanges()` for each entity.

```csharp
using (var context = new BookStore())
{
    context.Database.Log = Console.WriteLine;
    Author author1 = new Author() {    Name = "Mark" };
    Author author2 = new Author() {    Name = "John" };
        
    context.Authors.Add(author1);
    context.SaveChanges();
    
    context.Authors.Add(author2);
    context.SaveChanges();
}
```

You can see that EF saves both entities in separate transactions.

```csharp
Opened connection at 10/29/2018 9:18:26 AM +00:00
Started transaction at 10/29/2018 9:18:26 AM +00:00

INSERT [dbo].[Authors]([Name])
VALUES (@0)
SELECT [AuthorId]
FROM [dbo].[Authors]
WHERE @@ROWCOUNT > 0 AND [AuthorId] = scope_identity()

-- @0: 'Mark' (Type = String, Size = -1)
-- Executing at 10/29/2018 9:18:26 AM +00:00
-- Completed in 3 ms with result: SqlDataReader

Committed transaction at 10/29/2018 9:18:26 AM +00:00
Closed connection at 10/29/2018 9:18:26 AM +00:00

Opened connection at 10/29/2018 9:18:26 AM +00:00
Started transaction at 10/29/2018 9:18:26 AM +00:00

INSERT [dbo].[Authors]([Name])
VALUES (@0)
SELECT [AuthorId]
FROM [dbo].[Authors]
WHERE @@ROWCOUNT > 0 AND [AuthorId] = scope_identity()

-- @0: 'John' (Type = String, Size = -1)
-- Executing at 10/29/2018 9:18:26 AM +00:00
-- Completed in 1 ms with result: SqlDataReader

Committed transaction at 10/29/2018 9:18:26 AM +00:00
Closed connection at 10/29/2018 9:18:26 AM +00:00
```

[Try it](https://dotnetfiddle.net/CeNHXv)

## Multiple Operations in Single Transaction

You can use multiple `SaveChanges` within a single transaction as shown below;

```csharp
using (var context = new BookStore())
{
    context.Database.Log = Console.WriteLine;
    using (DbContextTransaction transaction = context.Database.BeginTransaction())
    {
        try
        {
            Author author1 = new Author() {    Name = "Mark" };
            Author author2 = new Author() {    Name = "John" };
            
            context.Authors.Add(author1);
            context.SaveChanges();
            
            context.Authors.Add(author2);
            context.SaveChanges();
            transaction.Commit();
        }
        catch (Exception ex)
        {
            transaction.Rollback();
        }
    }
}
```

You can see that both author entities are saved to the database in one transaction.

```csharp
Opened connection at 10/29/2018 9:21:20 AM +00:00
Started transaction at 10/29/2018 9:21:20 AM +00:00

INSERT [dbo].[Authors]([Name])
VALUES (@0)
SELECT [AuthorId]
FROM [dbo].[Authors]
WHERE @@ROWCOUNT > 0 AND [AuthorId] = scope_identity()

-- @0: 'Mark' (Type = String, Size = -1)
-- Executing at 10/29/2018 9:21:20 AM +00:00
-- Completed in 3 ms with result: SqlDataReader

INSERT [dbo].[Authors]([Name])
VALUES (@0)
SELECT [AuthorId]
FROM [dbo].[Authors]
WHERE @@ROWCOUNT > 0 AND [AuthorId] = scope_identity()

-- @0: 'John' (Type = String, Size = -1)
-- Executing at 10/29/2018 9:21:20 AM +00:00
-- Completed in 0 ms with result: SqlDataReader

Committed transaction at 10/29/2018 9:21:20 AM +00:00
Closed connection at 10/29/2018 9:21:20 AM +00:00
```
[Try it online](https://dotnetfiddle.net/wDngnP)