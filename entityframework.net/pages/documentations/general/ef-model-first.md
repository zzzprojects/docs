---
PermaID: 1000103
Name: Model First
---

# EF Model First

## What is Entity Framework Model First? 

Model First allows you to create a new model using the Entity Framework Designer and then generate a database schema from the model. 

 - The model is stored in an EDMX file (.edmx extension) and can be viewed and edited in the Entity Framework Designer. 
 - The classes that you interact with in your application are automatically generated from the EDMX file.

## Create Model

Let's create a new empty project and add the EntityFramework NuGet package which is explained in the [Code First](/ef-code-first) approach.
 
We will use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.

Right click on the project in Solution Explorer and choose `Add > New Item...` option.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first1.png">

Select Data from the left menu and then `ADO.NET Entity Data Model`.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first2.png">

Enter **BookStore** as the name and click **Add**. This launches the Entity Data Model Wizard. Select **Empty FF Designer model** and click **Finish**.

The Entity Framework Designer is opened with a blank model. Now we can start adding entities, properties, and associations to the model.

Right-click on the design surface and select **Add New > Entity...**

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first3.png">

Enter `Book` as the entity name and `BlogId` as the key name and click `OK`.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first4.png">

Right-click on the new entity on the design surface and select **Add New > Scalar Property**, enter Title as the name of the property.

Similarly, add an `Author` entity with an `AuthorId` key and `FirstName` and `LastName` properties using the above-mentioned steps.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first5.png">

Right-click on the design surface and select **Add New > Association...**

Make one end of the relationship point to `Author` with a multiplicity of **One** and the other endpoint to `Books` with a multiplicity of **Many**. 

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first6.png">

It means that an `Author` has many `Books` and a `Book` belongs to one `Author`. Make sure to check the **Add foreign key properties to 'Book' Entity** box is checked and click `OK`.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first7.png">

We now have a simple model that we can generate a database from and use to read and write data.

## Generate Database

Entity Framework can calculate a database schema that will allow us to store and retrieve data using the above model.

Right-click on the design surface and select **Generate Database from Model...**. Click **New Connection...** and specify the Server and database name and click `OK`.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first8.png">

You will be asked if you want to create a new database, select **Yes**.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first9.png">

Select **Next* and the **Entity Framework Designer** will calculate a script to create the database schema.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first10.png">

Once the script is displayed, click **Finish** and the script will be added to your project and opened.

<img src="https://raw.githubusercontent.com/zzzprojects/EntityFramework-FAQ/master/docs2/images/model-first11.png">

Right-click on the script and select **Execute**, you will be prompted to specify the database to connect to.

## Access Data

Now that we have a model it's time to use it to access some data. The classes we are going to use to access data are being automatically generated for you based on the EDMX file.

```csharp
using (var db = new BookStoreContainer())
{
    db.Books.Add(new Book { Title = "Introduction to Programming" });
    db.SaveChanges();

    foreach (var book in db.Books)
    {
        Console.WriteLine(book.Title);
    }
}
```

### Related links

For further details please see the following links.

 - [https://www.tutorialspoint.com/entity_framework/entity_model_first_approach.htm](https://www.tutorialspoint.com/entity_framework/entity_model_first_approach.htm)
 - [http://www.entityframeworktutorial.net/model-first-with-entity-framework.aspx](http://www.entityframeworktutorial.net/model-first-with-entity-framework.aspx)