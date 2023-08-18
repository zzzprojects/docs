---
title: Concurrency Management in Entity Framework Core
description: Entity Framework Core provides support for optimistic concurrency management through configuration by data annotations and the fluent API
canonical: /concurrency
proficiencylevel: Intermediate
status: Published
lastmod: 2023-03-01
---

# EF Core Concurrency

Concurrency conflicts occur when one user retrieves an entity's data to modify it, and then another user updates the same entity's data before the first user's changes are written to the database. How you handle those conflicts depends on the nature of the changes being made.

### Last In Wins
In many cases, there is only one version of the truth, so it doesn't matter if one user's changes overwrite another's changes. In theory, the changes should result in the same update being made to the record. For example, it doesn't matter if two users attempt to update a sports fixture record with the final score.  There is no need for any concurrency management strategy in this scenario. This is known as the _last in-wins_ approach to concurrency control.


### Pessimistic Concurrency 

_Pessimistic concurrency_ involves locking database records to prevent other users from being able to access/change them until the lock is released, much like when two users attempt to open the same file on a network share. However, the ability to lock records is not supported by all databases and can be complex to the program as well as highly resource intensive. It is simply not practical at all in disconnected scenarios such as web applications. Entity Framework Core provides no support for pessimistic concurrency control.


### Optimistic Concurrency
_Optimistic concurrency_ assumes that the update being made will be accepted, but before the change is made in the database, the original values of the record are compared to the existing row in the database and if any changes are detected, a concurrency exception is raised. This is useful in situations where allowing one user's changes to overwrite another's could lead to data loss. This could happen for example, if two users are looking at a customer record, and one of the users adds a missing telephone number. The second user alters the address, but the record that they alter was retrieved before the telephone number was added by the first user. When the second user commits their change (which won't include the telephone number), the first change will be lost. Entity Framework Core provides support for optimistic concurrency management.

## Detecting Concurrency Conflicts

Entity Framework Core supports two approaches to concurrency conflict detection: configuring existing properties as concurrency tokens; and adding a "rowversion" property to act as a concurrency token.

### Configuring existing properties

Properties can be configured as concurrency tokens via data annotations by applying the [`ConcurrencyCheck` attribute](/configuration/data-annotation-attributes/concurrencycheck-attribute):

```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    [ConcurrencyCheck]
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}
```
Alternatively, properties can be configured using the Fluent API [`IsConcurrencyToken` method](/configuration/fluent-api/isconcurrencytoken-method):

```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
         modelBuilder.Entity<Author>()
            .Property(a => a.LastName).IsConcurrencyToken();
    } 
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
}
```
Any existing properties that have been configured as concurrency tokens will be included with their original values in the `WHERE` clause of an `UPDATE` or `DELETE` statement. When the SQL command is executed, EF Core expects to find one row that matches the original values. If any of the configured columns have had their values changed between the time that the data was retrieved and the time that the changes are sent to the database, EF Core will throw a `DbUpdateConcurrencyException` with the message:

> Database operation expected to affect 1 row(s) but actually affected 0 row(s). Data may have been modified or deleted since entities were loaded. 

The concurrency token configuration can be applied to as many non-primary key properties as needed. Care needs to be taken as this approach can lead to very long `WHERE` clauses, or a lot of data being passed into them especially if any of the properties being configured as concurrency tokens are unlimited string values as is illustrated where, where the `Biography` field has been included as a concurrency token:

![Entity Framework Core Concurrency Management](/images/23-09-2016-09-22-43.png)



### Adding a RowVersion property
The second approach to concurrency management involves adding a column to the database table to store a version stamp for the row of data.  Different database systems approach this requirement in different ways. SQL Server offers the `rowversion` data type for this purpose. The column stores an incrementing number. Each time the data is inserted or modified, the number increments. 

User A might retrieve a row of data, followed by User B. The `rowversion` value for the row will be the same for both users. If User B submits changes, the `rowversion` value in the table will increment by 1 for that row. If User A subsequently tries to modify the same record, the `rowversion` value in their `WHERE` clause combined with the primary key value will no longer match an existing row in the database and EF Core will throw a `DbUpdateConcurrencyException`.

A property must be a byte array data type to be mapped to a `rowversion` column. It can be configured to take part in concurrency checking by adding the `TimeStamp` data annotations attribute:
```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
    [TimeStamp]
    public byte[] RowVersion { get; set; }
}
```
If you prefer to use the Fluent API to configure the property, you will use the `IsRowVersion` method:  

```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
         modelBuilder.Entity<Author>()
            .Property(a => a.RowVersion).IsRowVersion();
    } 
}

public class Author
{
    public int AuthorId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public ICollection<Book> Books { get; set; }
    public byte[] RowVersion { get; set; }
}
```
Note that the `IsRowVersion` method was added in EF Core 1.1. It is a convenience method that simplifies the previous approach which was to combine the `IsConcurrencyToken` method with the `ValueGeneratedOnAddOrUpdate` method:
```csharp
public class SampleContext : DbContext
{
    public DbSet<Author> Authors { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
         modelBuilder.Entity<Author>()
            .Property(a => a.RowVersion)
            .IsConcurrencyToken()
            .ValueGeneratedOnAddOrUpdate();
    } 
}
```
Either approach results in the same thing - the `RowVersion` column will be configured as a database type that provides automatic row-versioning (e.g. `rowversion` in SQL Server), rather than a `varbinary` type, which is the default mapping for `byte` array types.

The `SaveChanges` method should be called within a `try-catch` block so that any 
`DbUpdateException` exceptions can be caught and the appropriate action taken, such as presenting the newly updated record to the user:
```csharp
try
{
    db.SaveChanges();
    // move on
}
catch (DbUpdateException)
{
    // get the latest version of the record for display
}
```

In a disconnected scenario such as a web application, you will most likely store the values of any concurrency tokens in hidden fields if they are not included as regular form fields. This is to ensure that they are available as parameter values to the `WHERE` clause of any `UPDATE` or `DELETE` statement. 
