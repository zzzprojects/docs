---
PermaID: 1000087
Name: Table per Type
---

# Table Per Type

## What is Inheritance Type in Entity Framework?  

Entity Framework code first creates tables for each concrete domain class. You can also design your domain classes using inheritance. 

 - In Object-oriented programming, we can include **"has a"** and **"is a"** relationship, whereas in a SQL-based relational model we have only a **"has a"** relationship between tables and there is no support for type inheritance.
 - So, how would you map object-oriented domain classes with the relational database?

## Answer

There are three approaches to represent an inheritance hierarchy in Entity Framework;

 - [Table per Concrete (TPC)](/tpc) 
 - [Table per Type (TPT)](/tpt)
 - [Table per Hierarchy (TPH)](/tph)

### Table per Type (TPT) 

Table-per-type inheritance uses a separate table in the database to maintain data for non-inherited properties and key properties for each type in the inheritance hierarchy.

 - Table per Type is about representing inheritance relationships as relational foreign key associations. 
 - Every class and subclass including abstract classes has its own table. 
 - The table for subclasses contains columns only for each noninherited property along with a primary key that is also a foreign key of the base class table.

#### TPT in Code First

We can create a TPT mapping simply by placing Table attribute on the subclasses to specify the mapped table name. Here is the very simple model which contains one abstract class `Person` and two non-abstract classes `Student` and `Teacher`. `Student` and `Teacher` classes inherit the Person class.


```csharp
public abstract class Person
{
    public int Id { get; set; }
    public string FullName { get; set; }
}

[Table("Students")]
public class Student : Person
{
    public DateTime EnrollmentDate { get; set; }
}

[Table("Teachers")]
public class Teacher : Person
{
    public DateTime HireDate { get; set; }
}

public class EntityContext : DbContext
{
    public DbSet<Person> People { get; set; }
}
```

If you prefer fluent API, then you can create a TPT mapping by using ToTable() method:

```csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Entity<Student>().ToTable("Students");
    modelBuilder.Entity<Teacher>().ToTable("Teachers");
}
```

Our TPT mapping is ready, and we can try adding new records to the database.

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
[Try it online]

As you can see, the base class and subclasses have its own table. The table for subclasses contains columns only for each noninherited property along with a primary key that is also a foreign key of the base class table. 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs/images/tpt-db-schema.png">

#### View Generated SQL

Let's examine SQL query that returns a list of all the students.

```csharp
var query = context.People.OfType<Student>().ToString();
```
[Try it online](https://dotnetfiddle.net/7bnLn8)

This query generated the following SQL statements that were executed in the database.

```csharp
SELECT 
    '0X0X' AS [C1], 
    [Extent1].[Id] AS [Id], 
    [Extent2].[FullName] AS [FullName], 
    [Extent1].[EnrollmentDate] AS [EnrollmentDate]
    FROM  [dbo].[Students] AS [Extent1]
    INNER JOIN [dbo].[People] AS [Extent2] ON [Extent1].[Id] = [Extent2].[Id]
```

#### TPT Advantages

 - The primary advantage of this strategy is that the SQL schema is normalized. 
 - Also, schema evolution is straightforward, and you can easily modify the base class or add a new subclass. 

For more information see [Inheritance with EF Code First: Part 2 - Table per Type (TPT)](https://weblogs.asp.net/manavi/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-2-table-per-type-tpt)