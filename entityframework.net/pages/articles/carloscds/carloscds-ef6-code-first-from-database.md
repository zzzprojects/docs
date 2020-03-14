---
Permalink: articles/carloscds-ef6-code-first-from-database
name: EF6 - Code First from Database!
tags: carloscds article code-first power-tools reverse-engineering
OriginalLink: http://carloscds.net/2014/07/entityframework-lendo-seu-banco-de-dados-com-o-visual-studio-2013-update-2/
CreatedDate: 2014-02-07
CreatedUserName: Carlos dos Santos
CreatedUserLink: http://carloscds.net/
---

# EntityFramework - Reading your database with Visual Studio 2013 Update 2

Some time ago, I made a post talking about how to [take advantage of your existing database with EntityFramework Power Tools](/articles/carloscds-ef6-harnessing-your-database), but the Power Tools model does not allow you to choose the tables or views you want to read, and sometimes this is very useful.

With the release of Visual Studio 2013 Update 2, and also EntityFramework 6.1, a new option was added in the data sources. It now allows making `reverse engineering` with Code First to choose which tables and views will be read, similar to what was already possible with the EntityFramework Designer.

To demonstrate this, let's create a new Console project in Visual Studio 2013:

<img src="http://carloscds.net/wp-content/uploads/2014/07/SNAGHTMLa64a79f_thumb.png" width="625" height="434">

Now we are going to do the reverse engineering, but using the new feature of Visual Studio 2013. To do this, in your project, right-click on the solution and then go to Add -> New Item:

<img src="http://carloscds.net/wp-content/uploads/2014/07/image_thumb1.png" width="538" height="240" border="0">

Now let's add an ADO.NET Entity Data Model:

<img src="http://carloscds.net/wp-content/uploads/2014/07/SNAGHTMLa676ebe_thumb.png" width="548" height="380">

And notice that there is now a new option called "Code First from database" and with it, we will read an existing database and create all the code for EF Code First.

<img src="http://carloscds.net/wp-content/uploads/2014/07/SNAGHTMLa69ebd4_thumb.png" width="509" height="456">

We will then choose this option and place the connection to our existing database: (in my example I am using the Microsoft database called `Northwind`)

<img src="http://carloscds.net/wp-content/uploads/2014/07/SNAGHTMLa69538c_thumb.png" width="507" height="455">

Now you can choose the tables and views you want for your Code First project:

<img src="http://carloscds.net/wp-content/uploads/2014/07/SNAGHTMLa6b3694_thumb.png" width="502" height="450">

All classes will be generated in Code First, but with a big difference with Power Tools: the mapping information is generated with Data Annotations instead of Fluent API. See the example of the `Product` class:

```csharp
public partial class Product
{
    [Key]
    public int ProductID { get; set; }

    [Required]
    [StringLength(40)]
    public string ProductName { get; set; }

    public int? SupplierID { get; set; }

    public int? CategoryID { get; set; }

    [StringLength(20)]
    public string QuantityPerUnit { get; set; }

    [Column(TypeName = "money")]
    public decimal? UnitPrice { get; set; }

    public short? UnitsInStock { get; set; }

    public short? UnitsOnOrder { get; set; }

    public short? ReorderLevel { get; set; }

    public bool Discontinued { get; set; }

    public virtual Categories Categories { get; set; }
}
```  
 
See that the class was generated, and Data Annotations was created.

Now just use EntityFramework Code First!