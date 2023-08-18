---
title: Lazy Loading Related Data In Entity Framework Core
description: Lazy Loading In Entity Framework Core 
canonical: /lazy-loading
status: Published
lastmod: 2023-02-11
---

# EF Core Lazy Loading

Lazy loading of data is a pattern whereby the retrieval of data from the database is deferred until it is needed. This sounds like a good thing, and in some scenarios, this can help to improve the performance of an application. In other scenarios, it can degrade the performance of an application substantially, particularly so in web applications. For this reason, lazy Loading was introduced in EF Core 2.1 as an opt-in feature.

## Enabling Lazy Loading

Lazy loading can be enabled in two ways:

- Using Proxies
- Using the `ILazyLoader` service

### Proxies

_Proxies_ are objects deriving from your entities that are generated at runtime by Entity Framework Core. These proxies have behavior added to them that results in database query being made as required to load navigation properties on demand. This was the default mechanism used to provide lazy loading in the previous version of Entity Framework. 

Enabling lazy loading by proxies requires three steps:

1. Install the _Microsoft.EntityFrameworkCore.Proxies_ package  
    ```cmd
    [Package Manager Console]
    install-package Microsoft.EntityFrameworkCore.Proxies
    [Dotnet CLI]
    add package Microsoft.EntityFrameworkCore.Proxies
    ```
2. Use the `UseLazyLoadingProxies` method to enable the creation of proxies in the `OnConfiguring` method of the `DbContext`:  
    ```csharp
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseLazyLoadingProxies();
    }
    ```
3. Make all navigation properties `virtual`:  
    ```csharp
    public class Author
    {
        public int AuthorId { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public virtual List<Book> Books { get; set; } = new List<Book>();
    }

    public class Book
    {
        public int BookId { get; set; }
        public string Title { get; set; }
        public int AuthorId { get; set; }
        public virtual Author Author { get; set; }
    }
    ```
This last step is the key to allowing EF Core to override your entities to create proxies. In addition, all entity types must be public, unsealed, and have a public or protected constructor.

### ILazyLoader

The `ILazyLoader` interface represents a component that is responsible for loading navigation properties if they haven't already been loaded. This approach circumvents the generation of proxies which isn't supported on all platforms. `ILazyLoader` can be used in one of two ways. It can be injected into the principal entity in the relationship, where it is used to load dependants. This requires that your model class(es) take a dependency on  `Microsoft.EntityFrameworkCore.Infrastructure`, which is available in the `Microsoft.EntityFrameworkCore.Abstractions` package. Or you can use a convention-based delegate. 

The following steps detail how to employ the first approach:

1. Install the _Microsoft.EntityFrameworkCore.Abstractions_ package into the project containing your model classes:    
    ```cmd
    [Package Manager Console]
    install-package Microsoft.EntityFrameworkCore.Abstractions
    [Dotnet CLI]
    add package Microsoft.EntityFrameworkCore.Abstractions
    ```

2. Alter the principal entity to include  
    - a `using` directive for `Microsoft.EntityFrameworkCore.Infrastructure`
    - a field for the `ILazyLoader` instance
    - an empty constructor, and one that takes an `ILazyLoader` as a parameter (which can be private, if you prefer)
    - a field for the collection navigation property
    - a getter in the public property that uses the `ILazyLoader.Load` method


        ```csharp
        using Microsoft.EntityFrameworkCore.Infrastructure;
 
        public class Author
        {
            private readonly ILazyLoader _lazyLoader;
 
            public Author()
            {
            }

            public Author(ILazyLoader lazyLoader)
            {
                _lazyLoader = lazyLoader;
            }

            private List<Book> _books;
            public int AuthorId { get; set; }
            public string FirstName { get; set; }
            public string LastName { get; set; }
            public List<Book> Books
            {
                get => _lazyLoader.Load(this, ref _books);
                set => _books = value;
            }
        }
        ```

Whether you have used proxies or the `ILazyLoader` interface, lazy loading is now enabled in your application, and will take place as soon as you reference dependent entities in a relationship:

```csharp
using(var db = new BookContext())
{
    var authors = db.Authors;
    foreach(var author in authors)
    {
        Console.WriteLine($"Name: {author.FirstName} {author.LastName}");
        foreach(var book in author.Books) // lazy loading initiated
        {
            Console.WriteLine($"\t{book.Title}");
        }
    }
}
```
If you add logging, you can see the SQL commands that are executed against the database (each one highlighted in yellow):

![Entity Framework Core Lazy Loading](/images/25-09-2018-07-22-08.png)

The first query retrieves the authors, and then there are _n_ more queries, where _n_ represents the number of results from the first query. This is known as the _n+1 query_ pattern, or probably more accurately, the _n+1 problem_. Flooding the database with unnecessary queries can cause performance problems. The same result set can be obtained with two queries using [`Include`](/dbset/querying-data#include-related-data):

```csharp
var authors = db.Authors.Include(a => a.Books);
foreach(var author in authors)
{
    Console.WriteLine($"Name: {author.FirstName} {author.LastName}");
    foreach(var book in author.Books)
    {
        Console.WriteLine($"\t{book.Title}");
    }
}
```
![Image](/images/25-09-2018-07-56-05.png)


The advice is not to use lazy loading unless you are certain that it is the better solution. This is why (unlike in previous versions of EF) lazy loading is not enabled by default in Entity Framework Core.


