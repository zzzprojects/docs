---
permaid: 1000216
Title: EF Core Migrations - Learn How to Use Common Command Line
MetaDescription: Unlock the power of EF Core with Migrations to understand existing command lines. Learn how to add a migration, update your database, and delete a migration.
LastMod: 2023-02-21
tags: code-first migration command-line
---

# EF Core Migrations: Discover How to Use Common Command Line

Migrations provide a way to incrementally apply schema changes to the database to keep it in sync with your EF Core model while preserving existing data in the database.

 - When start developing your applications, you will most likely see changes in your model with change in requirements. 
 - When there is a change in a model, we need to keep the database in sync with the model. 
 - The migrations feature enables you to make changes to your model and then propagate those changes to your database schema.

Let's say we have a simple model containing three entities.

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}

public class Invoice
{
    public int Id { get; set; }
    public DateTime Date { get; set; }

    public int CustomerId { get; set; }

    [ForeignKey("CustomerId")]
    public virtual Customer Customer { get; set; }
    public virtual List<InvoiceItem> Items { get; set; }
}

public class InvoiceItem
{
    public int InvoiceItemId { get; set; }
    public int InvoiceId { get; set; }
    public string Code { get; set; }

    [ForeignKey("InvoiceId")]
    public virtual Invoice Invoice { get; set; }
}
```

Here is the context class.

```csharp
public class MyContext : DbContext
{
    public DbSet<Customer> Customers { get; set; }
    public DbSet<Invoice> Invoices { get; set; }
    public DbSet<InvoiceItem> InvoiceItems { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder
            .UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;");
    }
}
```

#### Add Migration

You can use migration to create an initial database by adding initial migration command in **Package Manager Console**.

`PM> Add-Migration InitialCreate`

The **InitialCreate** is migration name, and it is up to you what you want to specify as a name. 

The initial migration commands will add three files to your project under the **Migrations** folder.

 - **_InitialCreate.cs:** It is the main migrations file which the operations necessary to apply the migration in `Up()` method and to revert it to `Down()` method.
 - **_InitialCreate.Designer.cs:** It is migrations metadata file and contains information used by EF.
 - **MyContextModelSnapshot.cs:** It is the snapshot of your model, and it used to determine what changed when adding the next migration.

Now to apply the migration to the database to create the schema, run the following command.

`PM> Update-Database`

You can see that database created with three tables. 

#### Add Another Migration

Now let's say you want to add a PhoneNumber property to the customer entity.

```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Address { get; set; }
    public string PhoneNumber { get; set; }
    public virtual List<Invoice> Invoices { get; set; }
}
```

After making changes to your EF Core model, the database schema will be out of sync. To bring it up to date, we need to add another migration by using the `Add-Migration` command and call this migration **AddPhoneNumber**. 

`PM> Add-Migration AddPhoneNumber`

Your migration file will look like this.

```csharp
public partial class AddPhoneNumber : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>(
            name: "PhoneNumber",
            table: "Customers",
            nullable: true);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(
            name: "PhoneNumber",
            table: "Customers");
    }
}
```

Apply the migration to the database using the following command.

`PM> Update-Database`

#### Remove Migration

Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it. To remove the last migration, you can use the following command.

`PM> Remove-Migration`

#### Revert Migration

If you already applied several migrations to the database but need to revert it, you can use the `Update-Database` command but also specify the name of the migration you want to roll back to.

`PM> Update-Database LastGoodMigration`
