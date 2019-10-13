---
PermaID: 1000049
Name: Async
---

# Async

In Entity Framework 6, asynchronous query and save are introduced using the `async` and `await` keywords that were introduced in .NET 4.5. 

 - The primary purpose of async programming is to freeing up the current managed thread to do other work while it waits for an operation that does not require any compute time from a managed thread.
 - It can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.

```csharp
using (var db = new BookStore())
{
    db.Authors.Add(new Author
    {
        FirstName = "Alan",
		LastName  = "Cuban"
    });
	
    Console.WriteLine("Calling SaveChanges.");
    await db.SaveChangesAsync();
    Console.WriteLine("SaveChanges completed.");

    var authors = await db.Authors.ToListAsync();

    foreach (var author in authors)
    {
        Console.WriteLine(author.FirstName + " " + author.LastName );
    }
}
```