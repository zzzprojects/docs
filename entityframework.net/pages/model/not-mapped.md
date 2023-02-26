---
PermaID: 1000118
Title: Entity Framework NotMapped Data Annotation
MetaDescription: Unlock the power of Entity Framework by using the NotMapped Data Annotations. Learn how to set this attribute to ignore the property from the mapping.
LastMod: 2023-02-25
Tags: code-first fluent-api data-annotations
---

# Entity Framework NotMapped Data Annotation

The `NotMapped` attribute is used to specify that an entity or property is not to be mapped to a table or column in the database. 

 - In EF, the default Code First conventions create a column for every property that is of a supported data type and which includes getters and setters. 
 - The `NotMapped` attribute overrides this default convention, and the entity or property will not be mapped to a table or column in the database. 

The following example will not be map the `AuditLog` class to a table in the database.

```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
}

[NotMapped]
public class AuditLog
{
    public int EntityId { get; set; }
    public int UserId { get; set; }
    public DateTime Modified { get; set; }
}
```

In this example, the `FullName` property will not be mapped to a column in the 'Authors' table in the database:

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    [NotMapped]
    public string FullName 
    {
    	get
    	{
            return FirstName + " " + LastName;
    	}
    }
}
```

[Try it](https://dotnetfiddle.net/WlzYdz)