---
PermaID: 1000098
Name: Migration
---

# Migration

The Migrations feature enables you to change the data model and deploy your changes to production by updating the database schema without having to drop and re-create the database. It is the recommended way to evolve your application's database schema if you are using the Code First workflow. 

Migrations provide a set of tools that allow:

 - Create an initial database that works with your EF model
 - Generating migrations to keep track of changes you make to your EF model
 - Keep your database up to date with those changes

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

## Update Database

Now let's change your model by adding another property to the `Book` class.

```csharp
public DateTime Date { get; set;}
``` 

Now If you rerun your application, you would get an `InvalidOperationException`. 

> The model backing the 'BlogContext' context has changed since the database was created. Consider using Code First Migrations to update the database ( http://go.microsoft.com/fwlink/?LinkId=238269).

It�s time to start using Code First Migrations. There are two kinds of Migration;

 - Automated Migration
 - Code-based Migration

### Automated Migration

To use Automated Migration, run the following command in the Package Manager Console.

```csharp
enable-migrations �EnableAutomaticMigration:$true
```

Once the command runs successfully, it creates an internal sealed Configuration class derived from `DbMigrationConfiguration` in the Migration folder in your project.

```csharp
namespace EFDemo.Migrations
{
    using System;
    using System.Data.Entity;
    using System.Data.Entity.Migrations;
    using System.Linq;

    internal sealed class Configuration : DbMigrationsConfiguration<EFDemo.BookContext>
    {
        public Configuration()
        {
            AutomaticMigrationsEnabled = true;
            ContextKey = "EFDemo.BookContext";
        }

        protected override void Seed(EFDemo.BookContext context)
        {
            //  This method will be called after migrating to the latest version.

            //  You can use the DbSet<T>.AddOrUpdate() helper extension method 
            //  to avoid creating duplicate seed data.
        }
    }
}
```

The next step is to set the database initializer in the context class.

```csharp
public class BookContext : DbContext
{
    public BookContext() : base("BookContext")
    {
        Database.SetInitializer(new MigrateDatabaseToLatestVersion<BookContext, EFDemo.Migrations.Configuration>());
    }

    public DbSet<Book> Books { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
    }
}
```

Now EF will automatically take care of the migration when you change the domain classes.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/migration2.png">

### Code-based Migration

In Code First Migrations, you need to execute the following commands in the Package Manager Console.

 - **Enable-Migrations**: Enables the migration in your project.
 - **Add-Migration**: It creates a new migration based on changes you have made to your model since the last migration was created.
 - **Update-Database** It applies any pending migrations to the database.

Let's add another property to your domain class.

```csharp
public string Publisher { get; set; }
```

Run the the following command in Package Manager Console.

```csharp
`Add-Migration AddPublisher`
```

In the Migrations folder, we now have a new `AddPublisher` migration. The migration filename is pre-fixed with a timestamp to help with ordering.

```csharp
namespace EFDemo.Migrations
{
    using System;
    using System.Data.Entity.Migrations;
    
    public partial class AddPublisher : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Books", "Publisher", c => c.String());
        }
        
        public override void Down()
        {
            DropColumn("dbo.Books", "Publisher");
        }
    }
}
```

We could now edit or add to this migration, but everything looks pretty good. Let�s use Update-Database to apply this migration to the database.

Run the `Update-Database` command in Package Manager Console.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/migration3.png">

You can see that the database is now up to date by adding the `Publisher` column in the `Books` table.