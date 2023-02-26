---
Permalink: articles/fixedbuffer-ef-core-data-access-easy-2
Title: Making Data Access Easy with Entity Framework Core (Part 2)
MetaDescription:  In this article, we will learn how to create a database model using Entity Framework Core with the "Code First" option, generating our database through classes that we will relate to each other. We will also explore how to use the CLI commands to generate migrations and update the database. This tutorial is ideal for anyone looking to make data access simple and easy using Entity Framework Core. Reading time: 10 minutes.
tags: fixedbuffer article data-access
OriginalLink: https://www.fixedbuffer.com/entity-framework-core-2/
CreatedDate: 2018-09-25
CreatedUserName: JorTurFer
CreatedUserLink: https://www.fixedbuffer.com/
LastMod: 2023-02-20
---

# Making Data Access Easy with Entity Framework Core (Part 2)

_Reading time: 10 minutes_

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/EFCore.png" alt="Entity Framework Core" width="591" height="591">

We come with the second part of [Making Data Access Easy with the Entity Framework Core](articles/fixedbuffer-ef-core-data-access-easy). This time, we are going to do the same example, but with the "Code First" option. Using this option, we will generate our database through classes that we will relate to each other so that we will see the two sides of the coin.

## Model

Let's have the objective clear, by means of the «Code First» option we are going to look for a model like this:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/modelo.jpg" alt="DB model" width="608" height="219">

In it we have 2 relationships 1 to many:

- A teacher may teach several courses.
- Each course can have several students.

## Creating the solution in Visual Studio

First, let's create the project, only this time, it will be a Net Core console project:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/consola-.net-core.jpg" alt=".net core console" width="955" height="655">

## Packages

Like the previous time, we installed the Entity Framework Core packages via NuGet:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/efc.jpg" alt="efc" width="813" height="84">
<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/tools-2.jpg" alt="tools" width="817" height="70">

In case you want to do it via console:

```package-manager
PM-> Install-Package Microsoft.EntityFrameworkCore -Version 2.1.3
PM-> Install-Package Microsoft.EntityFrameworkCore.Tools -Version 2.1.3
```

And the same for the MySql provider.

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/pomelo.jpg" alt="pomelo" width="802" height="69">

Or as always, by console:

```package-manager
PM-> Install-Package Pomelo.EntityFrameworkCore.MySql -Version 2.1.2
```

## Creating the solution in DotNet CLI

One of the advantages of NET Core is that it allows us to execute its actions from the command line, with that, we get all the functionality on platforms like Linux. In this case, we will use Powershell. To create a project, we will create a folder and navigate to it with:

```console
mkdir PostEntityCore
cd PostEntityCore
```

Once inside the folder, we will create the project and add the packages with:

```dotnet-console
dotnet new console
dotnet add package Microsoft.EntityFrameworkCore -v 2.1.3
dotnet add package Microsoft.EntityFrameworkCore.Tools -v 2.1.3
dotnet add package Pomelo.EntityFrameworkCore.MySql -v 2.1.2
```

## Generating the DbContext

In this case, we will generate the model through classes in our project, to organize the project, we will add a «Data» folder to the solution, where we will add the following classes:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/efccf.png" alt="Entity Framework Core" width="241" height="177">

We are going to break down the classes:

```csharp
//Alumnos.cs
using System;
using System.ComponentModel.DataAnnotations;

namespace PostCore.Data
{
    public class Alumnos
    {
        [Key]
        public int IdAlumno { get; set; } //Clave primaria
        public string Nombre { get; set; } 
        public DateTime Nacimiento { get; set; }
        public int IdCurso { get; set; } //Campo clave foranea

        //Entity Framewrok Core
        public Cursos Curso { get; set; } //Objeto de navegación virtual EFC
    }
}

//Cursos.cs
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace PostCore.Data
{
    public class Cursos
    {
        //Inicializamos el objeto de navegacion virtual de Entity Framework Core
        public Cursos()
        {
            Alumnos = new HashSet();
        }

        [Key]
        public int IdCurso { get; set; } //Clave primaria
        public string Nombre { get; set; }
        public string Ciudad { get; set; }
        public int IdProfesor { get; set; } //Campo clave foranea

        //Entity Framewrok Core
        public Profesores Profesor { get; set; } //Objeto de navegación virtual EFC
        public ICollection Alumnos { get; set; } //Objeto de navegación virtual EFC
    }
}

//Profesores.cs
using System.Collections.Generic;
using System.ComponentModel.DataAnnotations;

namespace PostCore.Data
{
    public class Profesores
    {
        //Inicializamos el objeto de navegacion virtual de Entity Framework Core
        public Profesores()
        {
            Cursos = new HashSet();
        }
        [Key]
        public int IdProfesor { get; set; } //Clave primaria
        public string Nombre { get; set; }

        //Entity Framewrok Core
        public ICollection Cursos { get; set; } //Objeto de navegación virtual EFC
    }
}

//PostDbContext.cs
using Microsoft.EntityFrameworkCore;

namespace PostCore.Data
{
    //Heredamos de DbContext nuestro contexto
    class PostDbContext : DbContext
    {
        //Constructor sin parametros
        public PostDbContext()       
        {
        }

        //Constructor con parametros para la configuracion
        public PostDbContext(DbContextOptions options)
        : base(options)
        {
        }

        //Sobreescribimos el metodo OnConfiguring para hacer los ajustes que queramos en caso de
        //llamar al constructor sin parametros
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            //En caso de que el contexto no este configurado, lo configuramos mediante la cadena de conexion
            if (!optionsBuilder.IsConfigured) 
            {
                optionsBuilder.UseMySql("Server=localhost;Database=postefcore;Uid=root;Pwd=root;");
            }
        }

        //Tablas de datos
        public virtual DbSet Alumnos { get; set; }
        public virtual DbSet Cursos { get; set; }
        public virtual DbSet Profesores { get; set; }
    }
}
```

## Entity Framework Core "Code First" in Visual Studio

Once we have the context created in our project, we will go to the "Package Manager Console", and there we will have these commands:

1. add-migration {name} -Context {context}
2. remove-migration
3. update-database {name} -Context {context}
4. drop-database -Context {context}

We will explain the commands:

`add-migration`: With this command, we will generate the migration that we will launch to the database. This command has 2 parameters:

1. {name}: With this parameter, we will indicate the name that we want to indicate to the migration, in addition, it is mandatory.
2. -Context {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

`remove-migration`: With this command, we will remove the last migration that we have generated. It can be used several times consecutively to eliminate migrations from the last to the first.

`update-database`: With this command, we will send the migration changes to the database, making it effective:

1. {name}: With this parameter, we will indicate the name of the migration that we want to apply.
2. -Context {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

`drop-database`: With this command, we will delete the database. This command has 1 parameter:

1. -Context {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

Once we know the commands, let's get down to business ... To start, we use the command:

```package-manager
add-migration init -Context PostDbContext
```

That generates a "Migrations" folder where you will be saving each of the migrations. **It is convenient not to delete the content**, since it stores the "versions" of the data context, so that we can revert the changes in the database. Also, the fact of storing these migrations allows the system to apply changes incrementally in the next migrations, and it is a requirement to be able to apply changes to the database without errors due to existing tables.

With that, we execute:

```package-manager
update-database -Context PostDbContext
```

## Entity Framework Core "Code First" in DotNet CLI

Likewise, we can do all the work from the dotnet cli with the equivalent commands of "**dotnet ef**" :

1. migrations add {name} -c {context}
2. migrations remove
3. database update {name} -c {context}
4. database drop -c {context}
We will explain the commands:


`migrations add`: With this command, we will generate the migration that we will launch to the database. This command has 2 parameters:

1. {name}: With this parameter, we will indicate the name that we want to indicate to the migration, in addition, it is mandatory.
2. -c {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

`migrations remove`: With this command, we will remove the last migration that we have generated. It can be used several times consecutively to eliminate migrations from the last to the first.

`database update`: With this command, we will send the migration changes to the database, making it effective:

1. {name}: With this parameter, we will indicate the name of the migration that we want to apply.
2. -c {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

`database drop`: With this command, we will delete the database. This command has 1 parameter:

1. -c {context}: In case you have more than one data context in our program, we will indicate which of the contexts is using this parameter. If we only have one data context, it is not mandatory to use it.

Once we know the CLI commands, let's go to it:

```dotnet-console
dotnet ef migrations add init -c PostDbContext
```

In this case, we must consider the same things as if we did it from Visual Studio.

With that, we execute:

```dotnet-console
dotnet ef database update -c PostDbContext
```

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/reverse.png" alt="Entity Framework Core">

Once this is done in any of the 2 ways, from the manager of our database, we will see that the database has been created, and if we apply reverse engineering to rebuild the model (from the MySql Workbench), we will see something like this:

We see that we have managed to generate the model we wanted, but we have one more table, "__efmigrationshistory", in this table is where the Entity Framework Core registers the versions of the context that are applied in the database.

## Example

In my [GitHub](https://github.com/FixedBuffer/PostEntityFrameworkCore-CodeFirst-) profile [I have left an example of the project](https://github.com/FixedBuffer/PostEntityFrameworkCore-CodeFirst-), which once applied the migrations on the database, we can see that it works correctly before the same program logic of the first part:

```csharp
using (postefcoreContext context = new postefcoreContext())
{
    //Creamos el profesor
    Profesores profesor = new Profesores() {Nombre = "Pedro" };
    context.Add(profesor);
    //Creamos los cursos
    Cursos curso1 = new Cursos() { Nombre = "Matematicas", IdProfesor = profesor.IdProfesor };
    context.Add(curso1);
    Cursos curso2 = new Cursos() { Nombre = "Lenguaje", IdProfesor = profesor.IdProfesor };
    context.Add(curso2);
    //Creamos los alumnos
    Alumnos alumno1 = new Alumnos() { Nombre = "Jorge", IdCurso = curso1.IdCurso };
    context.Add(alumno1);
    Alumnos alumno2 = new Alumnos() { Nombre = "Juan", IdCurso = curso1.IdCurso };
    context.Add(alumno2);
    Alumnos alumno3 = new Alumnos() { Nombre = "Andrea", IdCurso = curso2.IdCurso };
    context.Add(alumno3);
    Alumnos alumno4 = new Alumnos() { Nombre = "Sandra", IdCurso = curso2.IdCurso };
    context.Add(alumno4);
    //Guardamos los cambios
    context.SaveChanges();
}

using (postefcoreContext context = new postefcoreContext())
{
    var profesor = context.Profesores //Indicamos la tabla
                          .Include(x => x.Cursos) //Incluimos los resultados coincidentes de la tabla cursos (inner join)
                          .ThenInclude(x => x.Alumnos) //Incluimos los resultados coincidentes de la tabla alumnos (inner join)
                          .First(); //Seleccionamos el primero
    foreach (var curso in profesor.Cursos)
      foreach (var alumno in curso.Alumnos)
        Console.WriteLine($"El alumno {alumno.Nombre} recibe el curso de {curso.Nombre},impartido por {profesor.Nombre}");
}
```

It will return:

```console
The student Juan receives the Mathematics course, taught by Pedro
The Jorge student receives the Mathematics course, taught by Pedro
The Sandra student receives the Language course, taught by Pedro
The Andrea student receives the Language course, taught by Pedro
```

In case you don't have Visual Studio, we will launch the project from the dotnet CLI with:

```dotnet-console
dotnet run --project PostCore
```

## Notice to boaters
In case we do not want to store the migrations, they can be deleted from the project by deleting them as a file, or simply with:

- PM -> remove-migration
- CLI -> dotnet ef migrations remove

But every time we want to create a new migration and apply it, the previous migration will not exist, so we will have to drop the database by

- PM-> drop-database -Context PostDbContext
- CLI-> dotnet ef database dtop -c PostDbContext

Assuming with it the consequent loss of data. Otherwise, the update will throw an error indicating that the tables already exist in the database.

In development environments, it is not necessary to review the migration since, presumably, we do not care about the possible loss of data. Still, if we are going to apply migrations in production environments, we should review the files generated in the migration, where we will see that they are defined 2 methods:

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
protected override void Down(MigrationBuilder migrationBuilder)
```

These two methods will be executed during database updates ("Up" when applying a migration and "Down" when removing it). We should be sure that if, for example, we want to change how we store the data of a name, unifying two columns. We make a new column be generated, the data is migrated from the previous ones to the new one, and then the previous ones are deleted, so that we do not have a loss of data. In any case, whenever the migration has the possibility of a loss of information, the Entity Framework Core will notify us with the message:

<img src="https://www.FixedBuffer.com/wp-content/uploads/2018/09/mensaje.png" alt="Entity Framework Core">

_If after reading this entry, you are interested in the Entity Framework Core, [version 3.0 is already available and brings many new features](articles/fixedbuffer-ef-core-what-is-new)._