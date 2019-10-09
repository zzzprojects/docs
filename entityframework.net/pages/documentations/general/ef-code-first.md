---
PermaID: 1000106
Name: Code First
---

# EF Code First

## What is Entity Framework Code First? 

When you start learning Entity Framework, you will often see the term **Entity Framework Code First**. Code first is one of the three approaches to create an entity model.

## Answer

In Entity Framework, code first was introduced in Entity Framework 4.1. Code First modeling workflow targets a database that doesn't exist, and Code First will create it.

Code-First APIs will create the database on the fly based on your entity classes and configuration. It can also update the database if the model changes, using a feature called Code First Migrations.

 - Code First is a very popular approach and has full control over the code rather than database activity. 
 - In this approach, we can do all the database operations from the code and manual changes to database have been lost and everything is depending on the code.
 - In this you need to create POCO entities as data model.

## Initial Model & Database Using Code First Approach

Create a new application and install the EntityFramework NuGet package.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/migration.png">

Once the package is installed, add the following classes.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}
public class BookContext : DbContext
{
    public BookContext() : base("BookContext")
    {
    }

    public DbSet<Book> Books { get; set; }
}
```

It defines a single `Book` class that makes up our domain model and a `BookContext` class that is our EF Code First context. 

You can also specify the connection string in App.config file.

```csharp
<connectionStrings>
  <add name="BookContext" connectionString="Data Source=(localdb)\ProjectsV13;Initial Catalog=BookContext;" providerName="System.Data.SqlClient"/>
</connectionStrings>
```

Now that we have a model let's use it to perform data access.

```csharp
using System;
using System.Data.Entity;

namespace EFDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var db = new BookContext())
            {
                db.Books.Add(new Book { Title = "Introduction to Programming" });
                db.SaveChanges();

                foreach (var book in db.Books)
                {
                    Console.WriteLine(book.Title);
                }
            }
        }
    }

    public class Book
    {
        public int BookId { get; set; }
        public string Title { get; set; }
    }
    public class BookContext : DbContext
    {
        public DbSet<Book> Books { get; set; }
    }
}
```

Let's run your application, and you will see that the database is created automatically.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/migration1.png">

### Related links

For further details please see the following links.

 - [https://www.tutorialspoint.com/entity_framework/entity_framework_code_first_approach.htm](https://www.tutorialspoint.com/entity_framework/entity_framework_code_first_approach.htm)
 - [http://www.entityframeworktutorial.net/code-first/what-is-code-first.aspx](http://www.entityframeworktutorial.net/code-first/what-is-code-first.aspx)