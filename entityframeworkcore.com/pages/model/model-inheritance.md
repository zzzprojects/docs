---
permaid: 1000220
Title: EF Core Inheritance - Learn About TPC, TPH, and TPT Pattern
MetaDescription: Unlock the power of EF Core with Inheritance by understanding different approaches. Learn about TPC (Table Per Concrete), TPH (Table Per Hierarchy), and TPT (Table Per Type) approaches.
LastMod: 2023-02-21
tags: code-first inheritance fluent-mapping
---

# EF Core Inheritance: Discover About TPC, TPH, and TPT Pattern

## What is Inheritance Type in Entity Framework?  

Entity Framework code first creates tables for each concrete domain class. You can also design your domain classes using inheritance. 

 - In Object-oriented programming, we can include **"has a"** and **"is a"** relationship, whereas in a SQL-based relational model we have only a **"has a"** relationship between tables and there is no support for type inheritance.
 - So, how would you map object-oriented domain classes with the relational database?

## Answer

There are three approaches to represent an inheritance hierarchy in Entity Framework;

 - [Table per Hierarchy (TPH)](/tph)
 - [Table per Concrete (TPC)](/tpc) 
 - [Table per Type (TPT)](/tpt)

Entity Framework Core currently only implements the TPH pattern, but TPC and TPT are considered for inclusion, but no date has been decided yet.

### Table per Hierarchy (TPH) 

TPH inheritance uses one database table to maintain data for all of the entity types in an inheritance hierarchy.

 - An entire class hierarchy can be mapped to a single table. 
 - The table includes columns for all properties of all classes in the hierarchy. 
 - The concrete subclass represented by a particular row is identified by the value of a type discriminator column. 

Here is the very simple model which contains one abstract class `Person` and two non-abstract classes `Student` and `Teacher`. `Student` and `Teacher` classes inherit the Person class.


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
```

If you don't want to expose a DbSet for one or more entities in the hierarchy, you can use the Fluent API to ensure they are included in the model. And if you don't rely on conventions, you can specify the base type explicitly using HasBaseType.


```csharp
public class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=MyContextDb;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Teacher>().HasBaseType<Person>();
        modelBuilder.Entity<Student>().HasBaseType<Person>();
    }
}
```

Now to create a database using migrations from your model, run the following two command in Package Manager Console.

`PM> Add-Migration init`
`PM> Update-Database`

As you can see in the DB schema, Code First has added all the properties in one table and also added a discriminator column to distinguish between persistent classes. 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/inheritance.png" alt="inheritance">

TPH has one major problem, Columns for properties declared by subclasses will be nullable in the database.

 - For example, Code First created a (DATETIME, NULL) column to map HireDate property in Teacher class and EnrollmentDate in Student class.
 - Teacher instance won't have an EnrollmentDate property; the EnrollmentDate field must be NULL for that row.
 - Similarly, student instance won't have a HireDate property, so HireDate field must be NULL for that row.

For more information see [Inheritance (Relational Database)](https://docs.microsoft.com/en-us/ef/core/modeling/relational/inheritance)
