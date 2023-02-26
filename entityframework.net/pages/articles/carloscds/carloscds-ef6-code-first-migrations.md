---
Permalink: articles/carloscds-ef6-code-first-migrations
Title: Understanding Entity Framework Code First Migrations
MetaDescription: Learn how to use Entity Framework Code First Migrations and choose the best mode to manage your database updates.
LastMod: 2023-02-24
tags: carloscds article migration
OriginalLink: https://carloscds.net/2012/07/entity-framework-code-firstmigrations/
CreatedDate: 2012-07-15
CreatedUserName: Carlos dos Santos
CreatedUserLink: https://carloscds.net/
---

# Understanding Entity Framework Code First Migrations

One of the great features of Entity Framework Code First is the process of automatically updating the database through the feature called `Migrations`.

Migrations allow you, who already work with CodeFirst, to generate manageable updates in your database, or if you prefer, let Migrations itself take care of everything automatically, keeping your database always up to date with your classes.

In practice, we have two ways of working with Migrations:

1. Use migration points in the database, where we can move forward, applying the latest changes, or go back in the timeline, at any time in the database;
2. Use the fully automatic mode, where you don't have to worry about updating the database because when you run your program, this will be done automatically.

But which of the two models should I use in my application? And the answer is: It depends! If you need strict control of modifications, if you need to generate scripts that will be executed in your production environment, it is best to use the manual mode. But if what interests you is to keep the database always updated, without worrying about running scripts, then use the automatic mode.

In this article, we will cover the two methods of using Migrations, and after understanding each one, you will be able to choose the one that best suits your requirement.

## Creating the sample project

Before we start, let's create our sample project. I'm using Visual Studio 2012, but you can also use Visual Studio 2010.

Let's start by creating a console type project, using the .NET Framework 4:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb13.png" width="712" height="494" alt=" Create console type project">

Right after creating the project, we will add the Entity Framework Code First using [NuGet](https://nuget-tutorial.net/). To do this, open the NuGet manager in Tools -> Library Package Manager -> `Package Manager Console` and type:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb14.png" width="712" height="107" alt="Add EF Code first">

After that, we will have EF Code First installed in our project. We will now create a Context and a class so that we can work with Migrations.

That will be our Context class:

```csharp
public class Context : DbContext
{
    public DbSet<Client> Clients { get; set; }
}
```

And this will be our `Client` class:

```csharp
public class Client
{
    public int ID { get; set; }
    public string Name { get; set; }
}
```

We will also add an app.config file to identify our SQL server and the name of the database:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <connectionStrings>
        <add name="Context" providerName="System.Data.SqlClient" connectionString="data source=(local);initial catalog=ExampleMigrations;integrated security=true;"/>       
    </connectionStrings>
</configuration>
```

Now that we have our classes and the configuration file, let's play with Migrations.

## Migrations - Managing each update in the database

We will initially add Migrations to our project. Regardless of the method: manual or automatic, we need to add it to our project. We will do this using the Nuget window again using the `Enable-Migrations` command:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb22.png" width="712" height="178" alt="Add Migrations">

After this command, a new class will be added to our project, let name it `Configurations`:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb32.png" width="712" height="240" alt="name class">

As we will work with the manual process, we will leave this class as it is and we will start with the Migrations commands, which must be executed in the NuGet window:

Now, for all the changes we make to the classes, we will basically execute two commands:

```nuget-console
Add-Migrations
Update-DataBase
```

Add-Migrations "name_migrations" - creates a change in the database, where "name_migrations" is the name you will give for the update;

```nuget-console
Update-DataBase - apply changes to the database;
Update-DataBase - script - generates a script with SQL commands to be executed on the database.
```

For our example, we will run:

```nuget-console
Add-Migrations "CreateDatabase”
Update-DataBase
```

See that when running Add-Migrations, a new file was added to the project, containing the Migrations commands for the database:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb42.png" width="417" height="350" alt="ExemploMigrations">

```csharp
public partial class CreateDatabase : DbMigration
{
    public override void Up()
    {
        CreateTable(
            "Clients",
            c => new
                {
                    ID = c.Int(nullable: false, identity: true),
                    Name = c.String(),
                })
            .PrimaryKey(t => t.ID);
            
    }
        
    public override void Down()
    {
        DropTable("Clients");
    }
}
```

As our database did not yet exist, it was created after the Update-DataBase command:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb52.png" width="712" height="237" alt="Update-Database command">

To make our example more interesting, I will add some records to the database using the code below, but if you prefer, insert the data directly into SQL:

```csharp
static void Main(string[] args)
{
    var db = new Context();
    db.Clients.Add(new Client() { Name = "Carlos dos Santos" });
    db.Clients.Add(new Client() { Name = "Jose da Silva" });
    db.Clients.Add(new Client() { Name = "Antonio das Couves" });
    db.SaveChanges();
}
```

Now let's start to modify our class and see how migrations can really help us. Initially, we will add a property called `Active` in the `Client` class, and soon after we will create a new Migration to send this to the database:

`Client` class with the new property:

```csharp
public class Client
{
    public int ID { get; set; }
    public string Name { get; set; }
    public bool Active { get; set; }
}
```

Now when executing the command `Add-Migrations "Client_Active"`, we will have one more file of Migrations:

```csharp
public partial class Client_Active : DbMigration
{
    public override void Up()
    {
        AddColumn("Clients", "Active", c => c.Boolean(nullable: false));
    }

    public override void Down()
    {
        DropColumn("Clients", "Active");
    }
}
```

But before sending this to the database, let's imagine that you want to set a value for the `Active` property, for example, all the `Active` properties must be true. You can do this through the `Sql()` command inside the migrations file, see:

```csharp
public partial class Cliente_Active : DbMigration
{
    public override void Up()
    {
        AddColumn("Clients", "Active", c => c.Boolean(nullable: false));
        Sql("UPDATE Clients SET Active = 1");
    }

    public override void Down()
    {
        DropColumn("Clients", "Active");
    }
}
```

Obviously, you could have set this as a default value for the database, but the purpose here is to show you how to send commands during the Migrations process.

Now we will send the command to the database, but in a slightly different way, we will generate an SQL script. To do this, run the command as follows:

```nuget-console
Update-DataBase –script
```

And the result will be an SQL script file:

```sql
ALTER TABLE [Clientes] ADD [Ativo] [bit] NOT NULL DEFAULT 0
update clientes set ativo = 1
INSERT INTO [__MigrationHistory] ([MigrationId], [CreatedOn], [Model], [ProductVersion]) VALUES ('201207160033131_Cliente_Ativo', '2012-07-16T00:38:19.710Z', 0x1F8B0800000000000400CD56C96EDB3010BD17E83F083CB587988E0B0449202748ECB8085A3B4194E64E4B6387281755A40CFBDB7AE827F5173AD46E79C97AE84D2267797C7C33C3BFBFFFF8E74B29BC0524866BD527879D2EF140853AE26ADE27A99D1D1C93F3B38F1FFCAB482EBD87D2AEE7ECD053993E79B4363EA5D4848F2099E9481E26DAE899ED845A521669DAEB768FE9619702862018CBF3FCBB54592E21FBC1DF815621C4366562AC2310A658C79D208BEA4D980413B310FAE46A0932167ACCE709B308C610EF427086400210B317A2EA9E3854A4CA8719AF10995DDDAF62C8B2F6C9407050169A4668F60D566B0BB8749BE81812BBBA8359E17A3D241E5DF7A36DC7CAADE1E3B2E397B25F7AC49BA442B0A9C08519130688171F9D065627F01514200710DD326B21C16BB98E20435FB0701A1F3D8F8813DAED392228534ADB8CD60DE02D98132DA1041AD804E542BC115F42F41DD4DC3E5660C76C59AEE027F17E288EEA42279BA4D03C5CFEBF3FE985E50B5D66BDD45A00535B086A06F1697DA19BD78CC2B38C238DE55DE33F2CAD6E5D766E1C806D49C2AB63E7F2EB543BDB3054D96A7DD35CE06521D01D95E08F591C23C78DCA2856BC202F8BC141F072EDCB3C060DCD9612A8D0569950746C0EAD5D475A04239E183B64964D99BB814124B798EDE7B64CB3A7EAD6CAB3B477DFB9CF466BE8EC8852D337C2134934C90E0715969DF933DF206482255BEA76A0452AD5AEDADFE79D9753D33F5F797E84A2369A218AA5CD183E6D1DBFCD32DDA0B9D5C5DAB7B64FED6D932A7BA5FA96BAFD42694F0F830DE9E526C44372163C72B20B56C682EC38834EF04BE4F75A1B8C99E23330F65EFF0437D8B0325E3F50AA3E6A4C24FED3A9C2DDE99F9C1F7B7BEAFE69A0162C091F59F249B2E5E73775F829B7EFDCDDDB8DEACD6D3ED75B9F4453873AC7596C9A570F814DF9FBB4F962F28760F8BC0EE1DE4F0A42D7F2EAA0A5CDB59AE992643C5A135169D2BA8331581621431789E533165ADC0EC1986CC43F3091BA4E2BA7105DAB9BD4C6A9BD3006E454AC9AE7F5E9FEFCD9A45BC7ECDFC459D37E8F23204C8E47801B7599721155B8475B24B42384134B5121880A9F38186EBEAA224DB47A66A082BE21C4A05C7DDDBB2185C1CC8D0AD80276637B9AC375C6FC216738F96493C17CA5401230CCDC4881099A1E753EF7E0A7EEC57FF60FBDB90F33230C0000, '4.3.1')
```

Just run this file inside SQL, and the field will be created and updated, or if you prefer, run `Update-DataBase` again and, it will be done automatically.
This script feature can be useful if you need to update your application's environment and production as well.

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb62.png" width="425" height="264" alt="EF6 code first migrations">

Note: The `__MigrationHistory` table is used by Migrations to manage version history within the database.

## Going back to the database at a certain point:

A very interesting feature of Migrations is that you can return your database to any point where you have performed an `Add-Migrations`. Imagine then that we created the `active` column in the database and now you want to go back to the point where this column did not yet exist, for this we will execute the following Migrations command:

```nuget-console
Update-DataBase –target "CreateDatabase"
```

That brings our database back to the point that our case was the creation of the database, but it could be any other point. Remember that this command affects only the database and not our class.

Now let's see what it's like to run Migrations fully automatically.

## Migrations - Running updates automatically

Now that you know how to keep your database up to date by generating versions of the updates, let's imagine that you don't need to keep this history but simply keep the database up to date.

For this example, let's create a project with the same data as the previous example: create a console type project:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb72.png" width="712" height="494" alt="EF6 code first migrations">

Add EF Code First through the NuGet console and then add the context, the `client` class, and the `app.config` file, but in this file, we will change the database name to ExampleMigrationsAutomatic:

```csharp
public class Context : DbContext
{
    public DbSet<Client> Clients { get; set; }
}
```

```csharp
public class Client
{
    public int ID { get; set; }
    public string Name { get; set; }
}
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <connectionStrings>
        <add name="Context" providerName="System.Data.SqlClient" connectionString="data source=(local);initial catalog=ExampleMigrationsAutomatic;integrated security=true;"/>
    </connectionStrings>
</configuration>
```

Once this is done we will add the migrations, the same way as before, but now modifying the necessary parameters so that everything is automatic, then open the NuGet console and execute the command `Enable-Migrations`:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb82.png" width="712" height="178" alt="EF6 code first migrations">

Now that the differences begin. In the previous example, we did not modify anything in the configurations class, but in this case, we will make some adjustments:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb92.png" width="712" height="163" alt="EF6 code first migrations">

First, let's change the class to "public class", as we will need to reference it later. Then we will activate the automatic migration property, and finally, we will check the option that data can be lost during the migration. This last option is up to you because if you do not enable the option and Migrations is unable to update the database. You will receive an error.

And now we are going to modify the context constructor so that it calls the `DatabaseInitializer`, which makes the whole process happen:

```csharp
public class Context : DbContext
{
    public DbSet<Client> Clients { get; set; }

    public Context()
    {
        Database.SetInitializer(new MigrateDatabaseToLatestVersion<Context, Configuration>());
    }
}
```

What we did was to add the call to `DatabaseSetInitializer()` with the `MigrateDatabaseToLastVersion` option. Which makes our database always updated according to our classes.

Now just run the example below to create the database:

```csharp
static void Main(string[] args)
{
    var db = new Context();
    db.Clients.Add(new Client() { Name = "Carlos dos Santos" });
    db.Clients.Add(new Client() { Name = "Jose da Silva" });
    db.Clients.Add(new Client() { Name = "Antonio das Couves" });
    db.SaveChanges();
}
```

Now let's modify the client class and simply rerun the program:

```csharp
public class Client
{
    public int ID { get; set; }
    public string Name { get; set; }
    public string CPF { get; set; }
    public decimal Limit { get; set; }
}
```

After running the program and querying SQL:

<img src="https://www.carloscds.net/wp-content/uploads/2012/07/image_thumb102.png" width="519" height="337" alt="EF6 code first migrations">

Conclusion:

The Migrations feature is undoubtedly something really great in the Entity Framework Code First and certainly helps in the productivity of our daily lives. Choose the one that best suits you and start using it right now.