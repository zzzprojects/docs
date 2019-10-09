---
PermaID: 1000088
Name: Table per Hierarchy
---

# Table Per Hierarchy

## What is Inheritance Type in Entity Framework?  

Entity Framework code first creates tables for each concrete domain class. You can also design your domain classes using inheritance. 

 - In Object-oriented programming, we can include **"has a"** and **"is a"** relationship, whereas in a SQL-based relational model we have only a **"has a"** relationship between tables and there is no support for type inheritance.
 - So, how would you map object-oriented domain classes with the relational database?

## Answer

There are three approaches to represent an inheritance hierarchy in Entity Framework;

 - [Table per Concrete (TPC)](/tpc) 
 - [Table per Type (TPT)](/tpt)
 - [Table per Hierarchy (TPH)](/tph)

### Table per Hierarchy (TPH) 

TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.

 - An entire class hierarchy can be mapped to a single table. 
 - The table includes columns for all properties of all classes in the hierarchy. 
 - The concrete subclass represented by a particular row is identified by the value of a type discriminator column. 

#### TPH in Code First

You don't have to do anything special in Code First to enable TPH; it's the default inheritance mapping strategy. Here is the very simple model which contains one abstract class `Person` and two non-abstract classes `Student` and `Teacher`. `Student` and `Teacher` classes inherit the Person class.

```csharp
public abstract class Person
{
    public int Id { get; set; }
    public string FullName { get; set; }
}

public class Student : Person
{
    public DateTime EnrollmentDate { get; set; }
}

public class Teacher : Person
{
    public DateTime HireDate { get; set; }
}

public class EntityContext : DbContext
{
    public DbSet<Person> People { get; set; }
}
```

Let's add some new records to the database.


```csharp
using (var context = new EntityContext())
{
    Student student = new Student()
    {
        FullName = "Mark",
        EnrollmentDate = DateTime.Now
    };

    Teacher teacher = new Teacher()
    {
        FullName = "John",
        HireDate = DateTime.Now
    };

    context.People.Add(student);
    context.People.Add(teacher);

    context.SaveChanges();
}
```
[Try it online](https://dotnetfiddle.net/ZqCM2Q)

As you can see in the DB schema, Code First has added all the properties in one table and also added a discriminator column to distinguish between persistent classes. 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs/images/tph-db-schema.png">

TPH has one major problem, Columns for properties declared by subclasses will be nullable in the database.

 - For example, Code First created a (DATETIME, NULL) column to map HireDate property in Teacher class and EnrollmentDate in Student class.
 - Teacher instance won't have an EnrollmentDate property; the EnrollmentDate field must be NULL for that row.
 - Similarly, student instance won't have a HiretDate property, so HiretDate field must be NULL for that row.

#### View Generated SQL

Let's examine SQL query that returns a list of all the students and teachers.

```csharp
var query = context.People.ToString();
```
[Try it online](https://dotnetfiddle.net/QcKqqG)

This query generated the following SQL statements that were executed in the database.


```csharp
SELECT 
    [Extent1].[Discriminator] AS [Discriminator], 
    [Extent1].[Id] AS [Id], 
    [Extent1].[FullName] AS [FullName], 
    [Extent1].[EnrollmentDate] AS [EnrollmentDate], 
    [Extent1].[HireDate] AS [HireDate]
    FROM [dbo].[People] AS [Extent1]
    WHERE [Extent1].[Discriminator] IN (N'Student',N'Teacher')
```

For more information see [Inheritance with EF Code First: Part 1 - Table per Hierarchy (TPH)](https://weblogs.asp.net/manavi/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-1-table-per-hierarchy-tph)