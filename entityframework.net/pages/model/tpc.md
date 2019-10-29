---
PermaID: 1000089
Name: Table per Concrete
---

# Table Per Concrete

## What is Inheritance Type in Entity Framework?  

Entity Framework code first creates tables for each concrete domain class. You can also design your domain classes using inheritance. 

 - In Object-oriented programming, we can include **"has a"** and **"is a"** relationship, whereas in a SQL-based relational model we have only a **"has a"** relationship between tables and there is no support for type inheritance.
 - So, how would you map object-oriented domain classes with the relational database?

## Answer

There are three approaches to represent an inheritance hierarchy in Entity Framework;

 - [Table per Concrete (TPC)](/tpc) 
 - [Table per Type (TPT)](/tpt)
 - [Table per Hierarchy (TPH)](/tph)

### Table per Concrete (TPC) 

In Table per Concrete type, we use exactly one table for each (nonabstract) class and all the properties including inherited properties of a class can be mapped to columns of the table.

#### TPC in Code First

Here is the very simple model which contains one abstract class `Person` and two non-abstract classes `Student` and `Teacher`. `Student` and `Teacher` classes inherit the Person class.


```csharp
public abstract class Person
{
    [DatabaseGenerated(DatabaseGeneratedOption.None)]
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
```

Now let's specify a separate table for each of the subclasses and tell Entity Framework Code First that map all inherited properties to this table as well.


```csharp
public class EntityContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Student>().Map(m =>
        {
            m.MapInheritedProperties();
            m.ToTable("Students");
        });

        modelBuilder.Entity<Teacher>().Map(m =>
        {
            m.MapInheritedProperties();
            m.ToTable("Teachers");
        });
    }
}
```

#### Important Note:

 - SQL Server's `int` identity columns don't work very well together with TPC since there will be duplicate entity keys when inserting in subclasses tables with all having the same identity seed.
 - Therefore, to solve this, either a spread seed (where each table has its initial seed value) will be needed, or a mechanism other than SQL Server's int identity should be used.
 - So in this example, we need to take the responsibility of providing unique keys when inserting records into the database.
 - We can switch off identity simply by placing `DatabaseGenerated` attribute on the primary key property and pass `DatabaseGeneratedOption.None` to its constructor.

`EntityMappingConfiguration` class has **MapInheritedProperties** method which is responsible for all the mapping. Our TPC mapping is ready, and we can try adding new records to the database.



```csharp
using (var context = new EntityContext())
{
    Student student = new Student()
    {
        Id = 1,
        FullName = "Mark",
        EnrollmentDate = DateTime.Now
    };

    Teacher teacher = new Teacher()
    {
        Id = 2,
        FullName = "John",
        HireDate = DateTime.Now
    };

    context.People.Add(student);
    context.People.Add(teacher);

    context.SaveChanges();
}
```

[Try it online](https://dotnetfiddle.net/IwlxO2)

As you can see, the SQL schema is not aware of the inheritance. We have mapped two unrelated tables to a more expressive class structure. 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs/images/tpc-db-schema.png">

If the base class were concrete, then an additional table would be needed to hold instances of that class.

#### View Generated SQL

Let's examine SQL for a simple query in TPC mapping. 


```csharp
var query = context.People.ToString();
```

This query generated the following SQL statements that is being executed in the database.


```csharp
SELECT 
    CASE WHEN ([UnionAll1].[C2] = 1) THEN '0X0X' ELSE '0X1X' END AS [C1], 
    [UnionAll1].[Id] AS [C2], 
    [UnionAll1].[FullName] AS [C3], 
    CASE WHEN ([UnionAll1].[C2] = 1) THEN [UnionAll1].[C1] END AS [C4], 
    CASE WHEN ([UnionAll1].[C2] = 1) THEN CAST(NULL AS datetime2) ELSE [UnionAll1].[HireDate] END AS [C5]
    FROM  (SELECT 
        [Extent1].[Id] AS [Id], 
        [Extent1].[FullName] AS [FullName], 
        CAST(NULL AS datetime2) AS [C1], 
        [Extent1].[HireDate] AS [HireDate], 
        cast(0 as bit) AS [C2]
        FROM [dbo].[Teachers] AS [Extent1]
    UNION ALL
        SELECT 
        [Extent2].[Id] AS [Id], 
        [Extent2].[FullName] AS [FullName], 
        [Extent2].[EnrollmentDate] AS [EnrollmentDate], 
        CAST(NULL AS datetime2) AS [C1], 
        cast(1 as bit) AS [C2]
        FROM [dbo].[Students] AS [Extent2]) AS [UnionAll1]
```
[Try it online](https://dotnetfiddle.net/zypIN0)

For more information see [Inheritance with EF Code First: Part 3 - Table per Concrete Type (TPC)](https://weblogs.asp.net/manavi/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-3-table-per-concrete-type-tpc-and-choosing-strategy-guidelines)