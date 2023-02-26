---
permaid: 1000221
Title: EF Core Relationships - Learn About Existing Relation in Your Entity
MetaDescription: Unlock the power of EF Core with Relationships by understanding all different types. Learn about one-to-one, one-to-many, and many-to-many relationship.
LastMod: 2023-02-21
tags: code-first relationship fluent-mapping
---

# EF Core Relationships: Discover About Existing Relation in Your Entity

In relational databases, a relationship exists between two tables through foreign keys. A Foreign Key is a column or combination of columns that are used to establish and enforce a link between the data in those two tables. Entity framework Core supports three types of relationships;

 - One-to-Many
 - One-to-One
 - Many-to-Many 

### One-to-Many Relationship

In a one-to-many relationship, each table has a primary key that uniquely defines each row within the table. The easiest way to configure a one-to-many relationship is by convention. EF Core will create a relationship if an entity contains a navigation property. Therefore, the minimum required for a relationship is the presence of a navigation property in the principal entity.


```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public List<Book> Books { get; set; }
}

public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```

The `Author` class contains a **Books** navigation property which is a list of Book objects, while the `Book` class also has a navigation property **Author**. Most of the time, one-to-many relationships in an Entity Framework Core model follow conventions and require no additional configuration. Now when you run the migration, you will see the following code in migration file which will create the database.


```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Authors",
        columns: table => new
        {
            AuthorId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            Name = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Authors", x => x.AuthorId);
        });

    migrationBuilder.CreateTable(
        name: "Books",
        columns: table => new
        {
            BookId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            AuthorId = table.Column<int>(nullable: false),
            Title = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Books", x => x.BookId);
            table.ForeignKey(
                name: "FK_Books_Authors_AuthorId",
                column: x => x.AuthorId,
                principalTable: "Authors",
                principalColumn: "AuthorId",
                onDelete: ReferentialAction.Cascade);
        });

    migrationBuilder.CreateIndex(
        name: "IX_Books_AuthorId",
        table: "Books",
        column: "AuthorId");
}
```

Now, look at the database in SQL Server Object Explorer.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/relationships1.png" alt="relationships-1"> 

 - The primary key in Authors table is AuthorId, while in Books table the primary key is BookId. 
 - The AuthorId column in the Books table is a Foreign Key (FK), linking a book to its author. 
 - A book is a dependent entity in the relationship, and Author becomes the principal entity. 
 - Using foreign keys, you can link one author row in the database to many book rows. 
 
Now if your model does not follow the default conventions, the Fluent API can be used to configure the correct relationship between entities. 

 - When configuring relationships with the Fluent API, you will use the Has/With pattern. 
 - The **"Has"** side of the pattern is represented by the `HasOne` and `HasMany` methods. 
 - The **"With"** side of the relationship is represented by the `WithOne` and `WithMany` methods.


```csharp
protected override void OnModelCreating(Modelbuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasMany(a => a.Books)
        .WithOne(b => b.Author);
}
```

### One-to-One Relationship

In a one-to-one relationship, each row of data in one table is linked to zero or one row in the second table. 


```csharp
public class Author
{
    public int AuthorId { get; set; }
    public string Name { get; set; }
    public AuthorBiography Biography { get; set; }
}
+-
public class AuthorBiography
{
    public int AuthorBiographyId { get; set; }
    public string Biography { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string PlaceOfBirth { get; set; }
    public string Nationality { get; set; }
    public int AuthorId { get; set; }
    public Author Author { get; set; }
}
```

The `Author` class contains a **Biography** navigation property and the `AuthorBiography` class has a navigation property **Author**. Now when you run the migration, you will see the following code in migration file which will create the database.


```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Authors",
        columns: table => new
        {
            AuthorId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            Name = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Authors", x => x.AuthorId);
        });

    migrationBuilder.CreateTable(
        name: "AuthorBiographies",
        columns: table => new
        {
            AuthorBiographyId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            AuthorId = table.Column<int>(nullable: false),
            Biography = table.Column<string>(nullable: true),
            DateOfBirth = table.Column<DateTime>(nullable: false),
            Nationality = table.Column<string>(nullable: true),
            PlaceOfBirth = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_AuthorBiographies", x => x.AuthorBiographyId);
            table.ForeignKey(
                name: "FK_AuthorBiographies_Authors_AuthorId",
                column: x => x.AuthorId,
                principalTable: "Authors",
                principalColumn: "AuthorId",
                onDelete: ReferentialAction.Cascade);
        });

    migrationBuilder.CreateIndex(
        name: "IX_AuthorBiographies_AuthorId",
        table: "AuthorBiographies",
        column: "AuthorId",
        unique: true);
}
```

Now, look at the database in SQL Server Object Explorer.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/relationships2.png" alt="relationships-2"> 

Now if your model does not follow the default conventions, the Fluent API can be used to configure the correct relationship between entities.


```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Author>()
        .HasOne(a => a.Biography)
        .WithOne(b => b.Author);
}
```

### Many-to-Many Relationship

In a many-to-many relationship, each row of data in one table is linked to many rows in the second table and vice versa. For example, a book can appear in multiple categories and a category can contain many books. 

In EF Core, many-to-many relationships are not yet supported without an entity class to represent the join table. 


```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public List<Category> Categories { get; set; }
}

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public List<Book> Books { get; set; }
}
```

You can represent a many-to-many relationship by including another entity class for the join table and mapping two separate one-to-many relationships.


```csharp
public class Book
{
    public int BookId { get; set; }
    public string Title { get; set; }
    public List<BookCategory> BookCategories { get; set; }
}

public class Category
{
    public int CategoryId { get; set; }
    public string CategoryName { get; set; }
    public List<BookCategory> BookCategories { get; set; }
}

public class BookCategory
{
    public int BookId { get; set; }
    public Book Book { get; set; }
    public int CategoryId { get; set; }
    public Category Category { get; set; }
}
```

The Book and BookCategory have one-to-many relationship and Category and BookCategory have also one-to-many relationship. Now we need to configure the relationship using Fluent API.


```csharp
public class MyContext : DbContext
{
    public DbSet<Book> Books { get; set; }
    public DbSet<Category> Categories { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=BookStoreDB;");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<BookCategory>()
            .HasKey(bc => new { bc.BookId, bc.CategoryId });

        modelBuilder.Entity<BookCategory>()
            .HasOne(bc => bc.Book)
            .WithMany(b => b.BookCategories)
            .HasForeignKey(bc => bc.BookId);

        modelBuilder.Entity<BookCategory>()
            .HasOne(bc => bc.Category)
            .WithMany(c => c.BookCategories)
            .HasForeignKey(bc => bc.CategoryId);
    }
}
```

Now when you run the migration, you will see the following code in migration file which will create three tables in the database.


```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Books",
        columns: table => new
        {
            BookId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            Title = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Books", x => x.BookId);
        });

    migrationBuilder.CreateTable(
        name: "Categories",
        columns: table => new
        {
            CategoryId = table.Column<int>(nullable: false)
                .Annotation("SqlServer:ValueGenerationStrategy", SqlServerValueGenerationStrategy.IdentityColumn),
            CategoryName = table.Column<string>(nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Categories", x => x.CategoryId);
        });

    migrationBuilder.CreateTable(
        name: "BookCategory",
        columns: table => new
        {
            BookId = table.Column<int>(nullable: false),
            CategoryId = table.Column<int>(nullable: false)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_BookCategory", x => new { x.BookId, x.CategoryId });
            table.ForeignKey(
                name: "FK_BookCategory_Books_BookId",
                column: x => x.BookId,
                principalTable: "Books",
                principalColumn: "BookId",
                onDelete: ReferentialAction.Cascade);
            table.ForeignKey(
                name: "FK_BookCategory_Categories_CategoryId",
                column: x => x.CategoryId,
                principalTable: "Categories",
                principalColumn: "CategoryId",
                onDelete: ReferentialAction.Cascade);
        });

    migrationBuilder.CreateIndex(
        name: "IX_BookCategory_CategoryId",
        table: "BookCategory",
        column: "CategoryId");
}
```

Now, look at the database in SQL Server Object Explorer.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFrameworkCore/master/docs/images/relationships3.png" alt="relationships-3"> 
