---
Permalink: articles/fixedbuffer-ef-core-data-access-easy
Title: Making Data Access Easy with Entity Framework Core
MetaDescription: Learn how to use Entity Framework Core for data access in this tutorial. Get step-by-step instructions on how to generate the Entity Framework Core DbContext, insert data, and read data using navigation properties.
tags: fixedbuffer article data-access
OriginalLink: https://www.fixedbuffer.com/acceso-a-datos-con-entity-framework-core/
CreatedDate: 2018-09-18
CreatedUserName: JorTurFer
CreatedUserLink: https://www.fixedbuffer.com/
LastMod: 2023-02-20
---

# Making data access easy with Entity Framework Core

_Reading time: 6 minutes_

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/EFCore.png" alt="Entity Framework Core">

Second blog entry and I come to tell you about another situation that occurred in my office ... Another colleague just got into a project of intensive use of databases in which he requires a tool to solve the problem.

In my day to day, I have worked a lot with [Entity Framework 6](https://docs.microsoft.com/ef/ef6/), the "classic", but some time ago I found [Entity Framework Core](https://docs.microsoft.com/ef/core/). Entity Framework Core started with a relatively limited set of tools, and there is no possible comparison with its "big brother" Entity Framework 6. Despite everything, Entity Framework Core was designed with the philosophy of the "[cloud](https://www.fixedbuffer.com/como-instalar-netcore-en-linux/)" era and was super optimized for the things he did. All this has changed with version 2.0 (I leave you a link with the comparison and set of utilities).

Currently, we have a tool in hand that, although it is not as mature as Entity Framework 6, it does have a maturity to take into account. An important point that it has is its possibility of cross-platform execution, which allows us to use it with .NET Framework or with .NET Core (and I love that!).

Let's get down to business! How did we solve this? As expected, with Entity Framework Core!!!. In this case, since the database model is available, we use the `Database First` option. Although there is also the `Code First` option, so that the database is generated from the code, and we will talk about it in another post…

## Model

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/modelo.jpg" alt="DB model">

For this case, I have created a model like this:

In it, we have 2 relationships 1 to many:

1. A teacher may teach several courses.
2. Each course can have several students.

In this case, I will use a MySql database for the example, since it is a free database.

Being clear about the model, let's go to the problem!

## Creating the solution

The first thing will be to create a console project in c # and .NET Framework (or .NET Core, whichever is preferred):

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/consola-.net-full-1.jpg" alt="console .net full">

### packages

With the project created, we are going to download the Entity Framework Core packages via NuGet:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/efc.jpg" alt="The picture shows the Entity Framework Core package">

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/tools-2.jpg" alt="The picture shows the Entity Framework Core Tools package">

In case you want to do it via console:

```package-manager
PM-> Install-Package Microsoft.EntityFrameworkCore -Version 2.1.3
PM-> Install-Package Microsoft.EntityFrameworkCore.Tools -Version 2.1.3
```

We just need to download the data provider, in this case, MySql, and for that, we are going to use an implementation that works very well with Entity Framework Core:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/pomelo.jpg" alt="The picture shows the pomelo package for Entity Framework Core">

Or as always, by console:

```package-manager
PM-> Install-Package Pomelo.EntityFrameworkCore.MySql -Version 2.1.2
```

### Generating the Entity Framework Core DbContext

Once we have everything installed, we will execute the generation of the context. For that, we will use the command `scaffold` in the Package Manager Console (No, the Entity Framework Core does not have an interface. But it does not require more than a few commands, come on, it will not be tough).

```package-manager
PM->scaffold-dbcontext "Server=localhost;Database=postefcore;Uid=root;Pwd=root;" Pomelo.EntityFrameworkCore.Mysql -outputdir Models -context PostDbContext
```

Let's analyze the arguments:

1. -dbcontext
2. -outputdir
3. -context

In `dbcontext`, we are passing the context that we want to use. We will use a connection string and an Entity Framework Core provider for that. In this case, Pomelo.EntityFrameworkCore.Mysql, if we used another, we would have to indicate it here.

In `outputdir` we indicate the path in our project where we want it to create the necessary classes to function.

In `context` we indicate the name that we want to give to our data context.

Once the execution finishes, we will see something like this in our project:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/Contexto.jpg" alt="Context">

In case what we want is to update the model because we have made changes to the db, we would do it with the following command:

```package-manager
PM->scaffold-dbcontext "Server=localhost;Database=postefcore;Uid=root;Pwd=root;" Pomelo.EntityFrameworkCore.Mysql -outputdir Models -context PostDbContext -force
```

In this case, we have added the `-force` argument so that it overrides what is there.

You can read more about the arguments [here](https://docs.microsoft.com/ef/core/miscellaneous/cli/powershell).

### Inserting data

Now that's done, we can now access the database through the ORM, and operate it as we need, for example, we are going to add 1 teacher, 2 courses and 4 students. To do this, we insert the following code in our program class:

```csharp
using (PostDbContext context = new PostDbContext())
{
    // Create teacher
    Teacher teacher = new Teacher() { Name = "Pedro" };
    context.Add(teacher);
    
    // Create course
    Course course1 = new Course() { Name = "Matematicas", TeacherID = teacher.TeacherID };
    context.Add(course1);
    Course course2 = new Course() { Name = "Lenguaje", TeacherID = teacher.TeacherID };
    context.Add(course2);
    
    // Create student
    Student student1 = new Student() { Name = "Jorge", CourseID = course1.CourseID };
    context.Add(student1);
    Student student2 = new Student() { Name = "Juan", CourseID = course1.CourseID };
    context.Add(student2);
    Student student3 = new Student() { Name = "Andrea", CourseID = course2.CourseID };
    context.Add(student3);
    Student student4 = new Student() { Name = "Sandra", CourseID = course2.CourseID };
    context.Add(student4);
    
    // Save
    context.SaveChanges();
}
```

Although the tables require IDs for the relationships, and the query is not run until `SaveChanges()` is called, the Entity Framework Core is capable of storing the relationships and assigning the corresponding IDs. This way, we avoid having to make a query to obtain the teacher's ID, before adding it to a course, or the id of a course before adding it to a student. That is a great advantage, since it avoids the need for a function or two queries to the database. On the other hand, when we execute the call to `SaveChanges()`, Entity Framework Core fills the ids data with the values ​​that have been assigned in the database.

### Reading data

Now, we are going to read data taking advantage of the navigation properties. With it, we are going to get a teacher. With the teacher, we will get all his students without having to execute several queries or a long `inner join`. For that, we execute the following code:

```csharp
using (PostDbContext context = new PostDbContext())
{
    var teacher = context.Teachers
                          .Include(x => x.Courses)
                          .ThenInclude(x => x.Students)
                          .First();
                          
    foreach (var course in teacher.Courses)
      foreach (var student in course.Students)
        Console.WriteLine($"The student {student.Name} receives the {course.Name} course, taught by {teacher.Name}");
}
```

(In order for us to use .Include, we must add the corresponding using)

```csharp
using Microsoft.EntityFrameworkCore;
```

Here is the console result:

```console
The student Juan receives the Mathematics course, taught by Pedro
The Jorge student receives the Mathematics course, taught by Pedro
The Sandra student receives the Language course, taught by Pedro
The Andrea student receives the Language course, taught by Pedro
```

Which we see coincides with the data we have entered, thus giving us this ORM a level of abstraction that can greatly facilitate our lives. Not to mention the possibility of doing all of this asynchronously. All this transparently to us, and without any additional effort.

It should be noted that although I have not expressly indicated it, the Entity Framework Core allows us to work with the tables in the database using LinQ. In this way, we can work with the database without using SQL statements, with a strongly typed tool.

In the [next post](/articles/fixedbuffer-ef-core-data-access-easy-2), we will talk about the `Code First` option, and we will delve into the benefits of using an Entity Framework Core.

_If after reading this entry, you are interested in the Entity Framework Core, [version 3.0 is already available and brings many new features](articles/fixedbuffer-ef-core-what-is-new)._