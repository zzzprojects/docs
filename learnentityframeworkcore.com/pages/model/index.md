---
title: The Entity Framework Core Model
description: An Entity Framework model is a conceptual model of an application's domain. The domain includes all topics relevant to the problem-solving areas of interest to the application users.
canonical: /model
status: Published
lastmod: 2025-07-13
---

# EF Core Model

An EF Core model is a conceptual model of an application's domain. The domain includes all topics relevant to the problem-solving areas of interest to the application's users. The model includes data and can also include behavior. Typically, models for CRUD applications don't tend to incorporate a lot of behavior.

Models are expressed as a collection of classes written in C#. Each class represents an _entity_ (a.k.a. business object, domain object) within the application domain.

The following example illustrates a minimal model for an application that is concerned with books and their authors:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string LastName { get; set; }
    public List<Book> Titles { get; set; } = new List<Book>();
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public Author Author { get; set; }
}
```
When working with Entity Framework Core, it is usual for the model structure to closely match the schema of the database. 

![Model Diagram](/images/22-02-2017-09-38-14.png) 

![Database Diagram](/images/17-08-2016-14-01-13.png)

If the model differs from the database schema, it can be mapped to the target tables and columns using the [Fluent API configuration](/configuration/fluent-api) capability of Entity Framework Core.

The model can be designed by hand coding, or it can be [reverse engineered from an existing database](/walkthroughs/existing-database).


