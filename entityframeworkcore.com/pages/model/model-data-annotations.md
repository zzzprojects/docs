---
permaid: 1000217
Title: EF Core Data Annotations - Learn Attribute to Map Entity To Model
MetaDescription: Unlock the power of EF Core with Data Annotations by mapping your entity to your model with attributes. Learn about the advantages of this approach and all possible data annotations.
LastMod: 2023-02-21
tags: code-first data-annotation fluent-mapping
---

# EF Core Data Annotations: Discover Attribute to Map Entity To Model

In .NET Framework, data annotation add extra meaning to the data by adding attribute tags. It is used to configure the classes which will highlight the most commonly needed configurations.

 - Data Annotations attributes are .NET attributes which can be applied to an entity class or properties to override default CodeFirst conventions in EF6 and EF Core.
 - The advantage of using Data Annotation feature is that by applying Data Attributes, we can manage the data definition in a single place and do not need re-write the same rules in multiple places.
 - It can be used in a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.

The mostly used Data Annotations attributes are as follows;

#### Key

You can use a **Key** attribute to configure a single property to be the key of an entity.


```csharp
public class OrderDetail
{
    [Key]
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }
    public Order Order { get; set; }
}
``` 

#### Concurrency Token (ConcurrencyCheck)

You can use the **ConcurrencyCheck** attribute to configure a property as a concurrency token.


```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public string Address { get; set; }

    [ConcurrencyCheck]
    public string Version { get; set; }
}
``` 

You can use any property, which you want to participate in the Concurrency Check.
#### NotMapped

You can use a **NotMapped** attribute to exclude a type from the model or any property of the entity.


```csharp
[NotMapped]
public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
``` 

OR


```csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    [NotMapped]
    public DateTime LoadedFromDatabase { get; set; }
}
``` 

#### Required

You can use a **Required** attribute to indicate that a property is required.


```csharp
public class Blog
{
    public int BlogId { get; set; }
    [Required]
    public string Url { get; set; }
}
``` 

#### MaxLength

You can use the **MaxLength** attribute to configure a maximum length for a property. 


```csharp
public class Person
{
    public int PersonId { get; set; }

    [MaxLength(50)]
    public string LastName { get; set; }

    [MaxLength(50)]
    public string FirstName { get; set; }
}
``` 


#### MinLength 

You can use the **MinLength** attribute to configure a minimum length for a property. 


```csharp
public class Person
{
    public int PersonId { get; set; }

    [MinLength(3)]
    public string LastName { get; set; }

    [MinLength(3)]
    public string FirstName { get; set; }
}
```

#### StringLength

You can use the **StringLength** attribute to specify additional property validations like MaxLength. The only difference is that StringLength attribute can only be applied to a string type property of Domain classes.


```csharp
public class Person
{
    public int PersonId { get; set; }

    [StringLength(50)]
    public string LastName { get; set; }

    [StringLength(50)]
    public string FirstName { get; set; }
}
``` 

#### ForeignKey

You can use the **Data ForeignKey** attribute to configure which property should be used as the foreign key property for a given relationship.


```csharp
public class OrderDetail
{
    public int OrderDetailID { get; set; }
    public int OrderID { get; set; }
    public int ProductID { get; set; }
    public int Quantity { get; set; }

    [ForeignKey("OrderID")]
    public Order Order { get; set; }
}

public class Order
{
    public int OrderID { get; set; }
    public int CustomerID { get; set; }
    public int EmployeeID { get; set; }
    public DateTime OrderDate { get; set; }
    public List<OrderDetail> OrderDetails { get; set; }
}
``` 

#### Timestamp

You can use the **Timestamp** attribute the same as ConcurrencyCheck attribute, but it will also ensure that the database field that code first generates is non-nullable.


```csharp
public class Person
{
    public int PersonId { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }

    [Timestamp]
    public byte[] TStamp { get; set; }
}
``` 

#### Table

You can use **Table** attribute to map the class name which is different from the table name in the database.


```csharp
[Table("UserInfo")]
public class Person
{
    public int PersonId { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}
``` 

#### Column

You can use **Column** attribute to map the property name which is different from the column name in the database. It is also the same as Table attribute, but Table attribute overrides the table behavior while **Column** attribute overrides the column behavior.


```csharp
public class Person
{
    public int PersonId { get; set; }

    [Column("LName")]
    public string LastName { get; set; }

    [Column("FName")]
    public string FirstName { get; set; }
}
``` 
