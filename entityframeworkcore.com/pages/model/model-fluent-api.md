---
permaid: 1000219
Title: EF Core Fluent API - Learn How to Map Entity To Model Using Code
MetaDescription: Unlock the power of EF Core with Fluent API by mapping your entity to your model with code. Learn about the advantages of this approach and how to use the model builder.
LastMod: 2023-02-21
tags: code-first fluent-mapping model
---

# EF Core Fluent API: Discover How to Map Entity To Model Using Code

Fluent API specify the model configuration that you can with data annotations as well as some additional functionality that can not be possible with data annotations. 

 - In Entity Framework Core, the ModelBuilder class acts as a Fluent API.
 - We can configure many different things by using it because it provides more configuration options than data annotation attributes.
 - Data annotations and the fluent API can be used together, but precedence of **Fluent API > data annotations > default conventions**.

You can override the `DbContext.OnModelCreating` method and use a parameter modelBuilder of type `ModelBuilder` to configure domain classes.


```csharp
public partial class StoreDBContext : DbContext
{
    public virtual DbSet<OrderDetails> OrderDetails { get; set; }
    public virtual DbSet<Orders> Orders { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        if (!optionsBuilder.IsConfigured)
        {
            optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=StoreDB;");
        }
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<OrderDetails>(entity =>
        {
            entity.HasKey(e => e.OrderDetailId);

            entity.HasIndex(e => e.OrderId);

            entity.Property(e => e.OrderDetailId).HasColumnName("OrderDetailID");

            entity.Property(e => e.OrderId).HasColumnName("OrderID");

            entity.Property(e => e.ProductId).HasColumnName("ProductID");

            entity.HasOne(d => d.Order)
                .WithMany(p => p.OrderDetails)
                .HasForeignKey(d => d.OrderId);
        });

        modelBuilder.Entity<Orders>(entity =>
        {
            entity.HasKey(e => e.OrderId);

            entity.Property(e => e.OrderId).HasColumnName("OrderID");

            entity.Property(e => e.CustomerId).HasColumnName("CustomerID");

            entity.Property(e => e.EmployeeId).HasColumnName("EmployeeID");
        });
    }
}
``` 

####  Key

You can use the **HasKey()** method to configure the name of the primary key constraint in the database.


```csharp
public class OrderDetail
{
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }
    public Order Order { get; set; }
}

class MyContext : DbContext
{
    public DbSet<OrderDetail> OrderDetails { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<OrderDetail>()
            .HasKey(b => b.OrderDetailID);
    }
}
```

#### Concurrency Token (ConcurrencyCheck)

You can use the **IsConcurrencyToken()** method to configure a property as a concurrency token.


```csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(p => p.LastName)
            .IsConcurrencyToken();
    }
}
```

#### Exclude Types or Properties

You can use the **Ignore()** method to exclude a type or a property from the model.


```csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Ignore<BlogMetadata>();
    }
}
``` 

OR


```csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Ignore(b => b.LoadedFromDatabase);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public DateTime LoadedFromDatabase { get; set; }
}
``` 

#### Required

You can use the **IsRequired()** method to indicate that a property is required.


```csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Url)
            .IsRequired();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
``` 

#### Table Mapping

You can use the **ToTable()** method to configure the table that a type maps to.


```csharp
public class Order
{
    public int OrderID { get; set; }
    public int CustomerID { get; set; }
    public int EmployeeID { get; set; }
    public DateTime OrderDate { get; set; }
}

class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>()
            .ToTable("OrdersData");
    }
}
``` 

#### Column Mapping

You can use the **HasColumnName()** method to configure the column to which a property is mapped.


```csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Order>()
            .Property(b => b.OrderID)
            .HasColumnName("Order_Id");
    }
}
```  

#### Foreign Key

You can use the **HasForeignKey()** method to configure the foreign key constraint name for a relationship.


```csharp
public partial class StoreDBContext : DbContext
{
    public virtual DbSet<OrderDetails> OrderDetails { get; set; }
    public virtual DbSet<Orders> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<OrderDetails>(entity =>
        {
            entity.HasKey(e => e.OrderDetailId);

            entity.HasIndex(e => e.OrderId);

            entity.Property(e => e.OrderDetailId).HasColumnName("OrderDetailID");

            entity.Property(e => e.OrderId).HasColumnName("OrderID");

            entity.Property(e => e.ProductId).HasColumnName("ProductID");

            entity.HasOne(d => d.Order)
                .WithMany(p => p.OrderDetails)
                .HasForeignKey(d => d.OrderId);
        });
    }
}
``` 

#### Default Schema

You can use the **HasDefaultSchema()** method to specify a default schema.


```csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        //Configure default schema
        modelBuilder.HasDefaultSchema("Admin");
    }
}
``` 

#### Computed Columns

You can use the **HasComputedColumnSql()** method to specify that a property should map to a computed column.


```csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(p => p.DisplayName)
            .HasComputedColumnSql("[LastName] + ', ' + [FirstName]");
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string DisplayName { get; set; }
}
``` 

#### Sequences

You can use the **HasSequence()** method to create a sequence in the model.


```csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
``` 

#### Default Values

You can use the **HasDefaultValue()** method to specify the default value for a property.


```csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .Property(b => b.Rating)
            .HasDefaultValue(3);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
    public int Rating { get; set; }
}
``` 

#### Index

You can use the **HasIndex()** method to configure the name of an index.


```csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .HasName("Index_Url");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
``` 

#### Alternate Keys

You can use the **HasAlternateKey()** method to configure the index and constraint name for an alternate key.


```csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasAlternateKey(c => c.LicensePlate)
            .HasName("AlternateKey_LicensePlate");
    }
}

class Car
{
    public int CarId { get; set; }
    public string LicensePlate { get; set; }
    public string Make { get; set; }
    public string Model { get; set; }
}
``` 
