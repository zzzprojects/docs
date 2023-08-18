---
title: Getting Started With Entity Framework Core - Console
description: How to get started using Entity Framework Core in a simple Console application
canonical: /walkthroughs/console-application
status: Published
lastmod: 2023-02-12
---
# Getting Started With EF Core - Console

This walkthrough demonstrates the minimum required to create a database using Entity Framework Core in a .Net Core Console application. The walkthrough assumes that you have [.Net Core SDK](https://www.microsoft.com/net/core) installed and that you have a suitable development environment/text editor. [Visual Studio Code](https://code.visualstudio.com/) will be used in this example.

## Creating a .NET Core Console application

If you have a version of Visual Studio that supports .Net Core (2017 or greater), you can use the project templates to create a new .Net Core console application. Alternatively, you can use a command line tool to create and build the project. You can use the Terminal that's integrated into Visual Studio Code for this. 

Create a folder named _EFCoreDemo_ and then open it in Visual Studio Code.

Press <kbd>Ctrl</kbd> + <kbd>'</kbd> to open a new Terminal window and then type the following commands:

```cmd
> dotnet new console        
> dotnet add package Microsoft.EntityFrameworkCore.SqlServer
> dotnet add package Microsoft.EntityFrameworkCore.Tools 
```
The commands above
- scaffolds a blank .NET Core console application
- adds the Entity FrameworkCore and EF Core tooling packages from Nuget to the project

Now modify the _.csproj_ file to include the following additional `<ItemGroup>` is added to the project. This may not have been added automatically:

```xml
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.1" />
  </ItemGroup>
```
Finally, run the following command to restore the packages and then test to see that the `ef` commands are available to the project:

```cmd
> dotnet restore      
> dotnet ef -h          
```

This should result in the initial help for the EF tools being displayed:

![EF commands Help](/images/02-03-2017-09-10-21.png)

Finally, build and run the application with the single command:

```cmd
> dotnet run
```

This results in a minimal console application: 

![.NET Core Console Application](/images/14-02-2017-14-23-29.png)

## Creating A Model

Add a new file named _EFCoreDemoContext.cs_ and add the following code to it:

```csharp
using Microsoft.EntityFrameworkCore;

namespace EFCoreDemo
{
    public class EFCoreDemoContext : DbContext
    {
        public DbSet<Book> Books { get; set; }
        public DbSet<Author> Authors { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(@"Server=.\;Database=EFCoreDemo;Trusted_Connection=True;MultipleActiveResultSets=true");
        }
    }
}
```

> **Note**: The model, particularly the DbContext class, is declared inside a `namespace`. If you don't place your context class in a namespace and you are working with EF Core versions before 2.1, you may come up against this [bug - now fixed in 2.1](https://github.com/aspnet/EntityFramework/issues/2467) when adding your migration.

Next, add a new file named _Author.cs_ and add the following code to it:

```csharp
using System.Collections.Generic;

namespace EFCoreDemo
{ 
    public class Author
    {
        public int AuthorId { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public ICollection<Book> Books { get; set; } = new List<Book>();
    }
}
```
Finally, add a new file named _Book.cs_ and add the following code to it:
```csharp
namespace EFCoreDemo
{ 
    public class Book
    {
        public int BookId { get; set; }
        public string Title { get; set; }
        public int AuthorId { get; set; }
        public Author Author { get; set; }
    }
}
```

This code defines classes for two entities - `Book` and `Author` that participate in a [fully defined one-to-many relationship](/conventions/one-to-many-relationship#fully-defined-relationship). The code also includes a class named `EFCoreDemoContext` that inherits from [DbContext](/dbcontext). This class has two [DbSet](/dbset) properties that represent the tables in the database (which are yet to be created). The `EFCoreDemoContext` class also includes a method named `OnConfiguring` where the connection string for a SQL Server database is defined. You should change this to suit your environment and database provider.


## Adding A Migration

[Migrations](/migrations) are used to keep the database schema in sync with the model. There is no database at the moment, so the first migration will create it and add tables for the entities represented by the `DbSet` properties on the `EFCoreDemoContext` that you added. 

Enter the following command in the Terminal window:

```cmd
dotnet ef  migrations add CreateDatabase
```
A new folder named Migrations is added to the project. It contains the code for the migration and a [model snapshot](/migrations/model-snapshot). 

![Migrations Folder](/images/01-03-2017-08-34-24.png)

Enter the following command to execute the migration:

```cmd
dotnet ef database update
```
The database is created but all of the string fields are unlimited in size (`nvarchar(MAX)` in SQL Server).

![Database created](/images/17-02-2017-07-56-26.png)

## Modifying The Database With Migrations

In the next section, you will modify the model to set limits to the size of selected string properties, and then use migrations to push those changes to the database schema.

Add the following to the `using` directives at the top of _Author.cs_ and _Book.cs_:
```csharp
using System.ComponentModel.DataAnnotations;
```
Modify the `Book` and `Author` entities so that they look like this:
```csharp
public class Book
{
    public int BookId { get; set; }
    [StringLength(255)]
    public string Title { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```
```csharp
public class Author
{
    public int AuthorId { get; set; }
    [StringLength(50)]
    public string FirstName { get; set; }
    [StringLength(75)]
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; } = new List<Book>();
}
```
In the Terminal, type the following command:

```cmd
dotnet ef migrations add LimitStrings
```

followed by 

```cmd
dotnet ef database update
```

This will scaffold a migration that will alter the size of the `Book`'s `Title` field and the `Author`'s `FirstName` and `LastName` fields. You should also get a warning that the scaffolded migration could lead to data loss. In this case, there is no data to be lost so the warning can be ignored.

Once the migration has been applied, the database schema will be altered. Here's how the modified `Author` table should look:

![Author Table](/images/17-02-2017-08-48-18.png)

## Working With Data

Open the _Program.cs_ file and replace the existing content with the following:
```csharp
using System.Collections.Generic;

namespace EFCoreDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var context = new EFCoreDemoContext())
            {
                var author = new Author {
                    FirstName = "William",
                    LastName = "Shakespeare",
                    Books = new List<Book>
                    {
                        new Book { Title = "Hamlet"},
                        new Book { Title = "Othello" },
                        new Book { Title = "MacBeth" }
                    }
                };
                context.Add(author);
                context.SaveChanges();
            }
        }
    }
}
```
Execute the following command from the command line:

```cmd
dotnet run
```

This will cause the code in the console application's `Main` method to execute, and the author and all books will be added to the database. You can verify this by querying the Books table using your preferred database management tool:

![Books table](/images/17-02-2017-10-56-32.png)

You can also verify it by changing the content of _Program.cs_ as follows:

```csharp
using static System.Console;

namespace EfCoreDemo
{
    class Program
    {
        static void Main(string[] args)
        {
            using (var context = new EFCoreDemoContext())
            {
                foreach(var book in context.Books)
                {
                    WriteLine(book.Title);
                }
            }
        }
    }
}
```
Then execute the following command from the Terminal:

```cmd
dotnet run
```
All going well, this should result in the titles of all three books being printed to the console window:

![Read Data](/images/02-03-2017-09-52-42.png)



