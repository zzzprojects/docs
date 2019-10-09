---
PermaID: 1000033
Name: How to see EF Erros
---

# How to see Entity Framework Errors

## How to see Entity Framework Errors?

There are certain cases where your program throws some exception, but with the exception message, you won't be able to understand what is actually causing this exception. For example, have a look at this simple example where we have Blog class.


```csharp
public class Blog
{
    public int BlogId { get; set; }
    [StringLength(10)]
    public string Name { get; set; }
    public string Url { get; set; }
}
```

Now let's add a blog to the database.


```csharp
using (var context = new EntityContext())
{
    var blog = new Blog()
    {
        Name = ".NET Framework Blog",
        Url = "https://blogs.msdn.microsoft.com/dotnet/"
    };

    context.Blogs.Add(blog);
    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/UZtI6h)

When you execute, you will see the following exception.
 
`System.Data.Entity.Validation.DbEntityValidationException: 'Validation failed for one or more entities. See 'EntityValidationErrors' property for more details.'`
 
Now you can see that the exception doesn't give you any hint about the actual problem.

### StackOverflow Related Questions

 - [Validation failed for one or more entities while saving changes to SQL Server Database using Entity Framework](https://stackoverflow.com/questions/5400530/validation-failed-for-one-or-more-entities-while-saving-changes-to-sql-server-da)
 - [Validation failed for one or more entities. See 'EntityValidationErrors' property for more details](https://stackoverflow.com/questions/7795300/validation-failed-for-one-or-more-entities-see-entityvalidationerrors-propert)
 - [Error message 'Unable to load one or more of the requested types. Retrieve the LoaderExceptions property for more information.'](https://stackoverflow.com/questions/1091853/error-message-unable-to-load-one-or-more-of-the-requested-types-retrieve-the-l)

## Answer

To see the actual error or the cause of the error, we need to use the catch the exception and then write out the errors to log file or console depending upon your application.


```csharp
using (var context = new EntityContext())
{
    try
    {
        var blog = new Blog()
        {
            Name = ".NET Framework Blog",
            Url = "https://blogs.msdn.microsoft.com/dotnet/"
        };

        context.Blogs.Add(blog);
        context.SaveChanges();
    }
    catch (DbEntityValidationException e)
    {
        foreach (var eve in e.EntityValidationErrors)
        {
            Console.WriteLine("Entity of type \"{0}\" in the state \"{1}\" has the following validation errors:",
                eve.Entry.Entity.GetType().Name, eve.Entry.State);
            foreach (var ve in eve.ValidationErrors)
            {
                Console.WriteLine("- Property: \"{0}\", Error: \"{1}\"",
                    ve.PropertyName, ve.ErrorMessage);
            }

            foreach (var ve in eve.ValidationErrors)
            {
                Console.WriteLine("- Property: \"{0}\", Value: \"{1}\", Error: \"{2}\"",
                    ve.PropertyName,
                    eve.Entry.CurrentValues.GetValue<object>(ve.PropertyName),
                    ve.ErrorMessage);
            }
        }
    }
}
```
[Try it online](https://dotnetfiddle.net/eu3eMK)

EntityValidationErrors is a collection which represents the entities which couldn't be validated successfully, and the inner collection ValidationErrors per entity is a list of errors on property level.

Now when the exception occurs, you will see the following message in your log file or console.


***Entity of type "Blog" in the state "Added" has the following validation errors:***
 - ***Property: "Url", Error: "The field Url must be a string with a maximum length of 10."***
 - ***Property: "Url", Value: "https://blogs.msdn.microsoft.com/dotnet/", Error: "The field Url must be a string with a maximum length of 10."***

These validation messages are usually helpful enough to find the source of the problem.