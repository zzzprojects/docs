---
PermaID: 1000113
Name: What is a DbContext
---

# What is DbContext

## What is DbContext? 

What is the purpose of a DbContext and why we need it in our application when using Entity Framework?

### StackOverflow Related Questions

 - [What is the purpose of a db context class in asp.net mvc](https://stackoverflow.com/questions/31953317/what-is-the-purpose-of-a-db-context-class-in-asp-net-mvc)

## Answer

DbContext is an important class in Entity Framework API. It is a bridge between your domain or entity classes and the database.

 - The primary class that is responsible for interacting with data as objects DbContext. 
 - The DbContext APIs is not released as part of the .NET Framework, Entity Framework team distributes EntityFramework.dll through NuGet to be more flexible and frequent with releasing new features to Code First and the DbContext APIs.
 - DbContext APIs simplify your application interaction with the database.
 - It also reduces the number of methods and properties you need to access commonly used tasks.
 - In previous versions of Entity Framework, these tasks were often complicated to discover and code.

### Define DbContext

 - The recommended way to work with context is to derive your class DbContext and expose the DbSet properties that represent collections of the specified entities in the context. 
 - If you are using the EF Designer, Entity Framework will generate the context. 
 - But if you are using the Code First approach, you will typically write the context yourself.


```csharp
public class MyContext : DbContext
{
    public MyContext() : base("name = MyContextDB") { }
    public virtual DbSet<Student> Students { get; set; }
    public DbSet<Course> Courses { get; set; }
}
```