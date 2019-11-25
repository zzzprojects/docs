---
PermaID: 1000205
Name: dapper-insert-and-update-in-bulk
---

# Dapper - Insert and Update in Bulk

A bulk operation is a single-target operation that can take a list of objects. A batch operation includes multiple target operations that each can take a list of objects. [Dapper Plus](http://dapper-plus.net/) extends the `IDbConnection` interface with Bulk Operations methods:

 - Bulk Insert
 - Bulk Update
 - Bulk Delete
 - Bulk Merge

## Bulk Insert

The `BulkInsert` method allows you to `INSERT` a large number of entities in your database.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{        
    connection.BulkInsert(list); 
}
```

Try it: [.NET Core](https://dotnetfiddle.net/GjoUpm) | [.NET Framework](https://dotnetfiddle.net/Ste0Sq)

## Bulk Update

The `BulkUpdate` method allows to `UPDATE` larger number of entities in a database table or a view.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{        
    var customersToUpdate = connection.Query<Customer>("Select TOP 3 * FROM CUSTOMERS").ToList();
    customersToUpdate.ForEach(x => x.Name += "_ExampleBulkUpdate");

    connection.BulkUpdate(customersToUpdate); 
}
```

Try it: [.NET Core](https://dotnetfiddle.net/JZfSWN) | [.NET Framework](https://dotnetfiddle.net/aAstP6)

## Bulk Delete

The `BulkDelete` method allows to `DELETE` larger number of entities in a database table or a view.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{        
    var customersToRemove = connection.Query<Customer>("Select TOP 3 * FROM CUSTOMERS").ToList();
            
    connection.BulkDelete(customersToRemove);
}
```

Try it: [.NET Core](https://dotnetfiddle.net/jTjPId) | [.NET Framework](https://dotnetfiddle.net/I0ETUe)

## Bulk Merge

The `BulkMerge` method allows to `MERGE` larger number of entities in a database table or a view.

```csharp
using (var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer()))
{        
    var customersToUpdate = connection.Query<Customer>("Select TOP 3 * FROM CUSTOMERS").ToList();
    customersToUpdate.ForEach(x => x.Name += "_ExampleBulkUpdate");
            
    var newCustomers = new List<Customer>()
    {
        new Customer() { Name ="New_Customer_1", Description = "Description", IsActive = true },
        new Customer() { Name ="New_Customer_2", Description = "Description", IsActive = true }
    };
            
    connection.BulkMerge(customersToUpdate, newCustomers); 
}
```

Try it: [.NET Core](https://dotnetfiddle.net/v7WtOI) | [.NET Framework](https://dotnetfiddle.net/4yltvn)

## Mapping

The `DapperPlusMapper` allows you to map the conceptual model (Entity) with the storage model (Database).

### Mapper - Table

The `Table()` sets the destination table or view name (including schema). By default, the name mapped is singular.

```csharp
DapperPlusManager.Entity<Customer>()
    .Table("tbl_Customers");
```

Try it: [.NET Core](https://dotnetfiddle.net/KCRMGQ) | [.NET Framework](https://dotnetfiddle.net/3GCxZX)

### Mapper - Identity

The `Identity()` sets column(s) which the database generates value. The value is outputted from the destination table (insert and merge action).

```csharp
DapperPlusManager.Entity<Customer>()
    .Table("Customers")
    .Identity(x => x.CustomerID);
```

Try it: [.NET Core](https://dotnetfiddle.net/KCRMGQ) | [.NET Framework](https://dotnetfiddle.net/d9fw17)

## Scenarios

Inserting thousands of entities for an initial load or a file importation is a typical scenario. Let's assume that we want to import customers' data provided in CSV format, and we have the following customers already in the database.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/display-all-customers.png">

The customer's data we want to import provided in CSV format is as follows;

```csharp
Sally, Whittaker,sally.whittaker@example.com
Belinda, Jameson, belinda.jameson@example.com
Jeff, Smith, jeff.smith@example.com
Sandy, Allen, sandy.allen@example.com
```

To import the customer's data, let's add the following model class.

```csharp
public class CustomerData
{
    [Display(Name = "CSV Data")]
    public string Text { get; set; }
}
```

The `CustomerData` class contains a Text property. Now we need to add an `ImportDataController` Controller which contains two `ImportCustomers` action methods.

 - The first method is for the `GET` process which will run when we call the method.
 - The second method is for the `POST` process which will run when we submit the entry to the server.

```csharp
public class ImportDataController : Controller
{
    // GET: ImportData
    public ActionResult ImportCustomers()
    {
        CustomerData data = new CustomerData()
        {
            Text = "Sally, Whittaker,sally.whittaker@example.com" + Environment.NewLine +
                   "Belinda, Jameson, belinda.jameson@example.com" + Environment.NewLine +
                   "Jeff, Smith, jeff.smith@example.com" + Environment.NewLine +
                   "Sandy, Allen, sandy.allen@example.com" + Environment.NewLine
        };
        return View(data);
    }

    [HttpPost]
    public ActionResult ImportCustomers(CustomerData data)
    {
        DapperPlusManager.Entity<Customer>().Table("Customers");

        try
        {
            List<AuditEntry> auditEntries = new List<AuditEntry>();
            List<Customer> customers = new List<Customer>();
            using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
            {                 
                using (var csv = new CsvReader(new StringReader(data.Text)))
                {
                    csv.Configuration.HasHeaderRecord = false;
                    while (csv.Read())
                    {
                        
                        string[] result = new string[3];
                        string value;

                        for (int i = 0; csv.TryGetField<string>(i, out value); i++)
                        {
                            result[i] = value;
                        }

                        Customer c = new Customer()
                        {
                            FirstName = result[0],
                            LastName = result[1],
                            Email = result[2]
                        };

                        customers.Add(c);
                    }
                }

                db.BulkInsert(customers);
            }

            return RedirectToAction("Index", "Customer");
        }
        catch (Exception e)
        {
            return View();
        }
    }
}
```

In the POST action, the customer's data is parsed and inserted into the database using `BulkInsert`. Let's create an empty view (ImportCustomers.cshtml) and add the following code.

```csharp
@model DapperDemoApp.Models.CustomerData

@{
    ViewBag.Title = "Import Customers";
}

<h2>Customers Data in CSV Format</h2>


@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    
    <div class="form-horizontal">
        <hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
        <div class="form-group">
            @Html.LabelFor(model => model.Text, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.TextAreaFor(model => model.Text, 20, 20, htmlAttributes: new { style = "width: 50%; max-width: 80%;" })
                @Html.ValidationMessageFor(model => model.Text, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            <div class="col-md-offset-2 col-md-10">
                <input type="submit" value="Save" class="btn btn-default" />
            </div>
        </div>
    </div>
}

<div>
    @Html.ActionLink("Back to List", "Index")
</div>

@section Scripts {
    @Scripts.Render("~/bundles/jqueryval")
}
```

[Try it online](https://dotnetfiddle.net/LAiJgP)

Let's update the menu options by changing the '_layout.cshtml' file, so that the user can view this data and also make some changes as needed.

```csharp
<div class="navbar-collapse collapse">
    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Customer")</li>
        <li>@Html.ActionLink("Import Data", "ImportCustomers", "ImportData")</li>
    </ul>
</div>

```

Now run your application and click on the Import Data menu option.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/import-data-1.png">

You can see by default, some more customers data is available in the text area, you can edit that but make sure it remains in CSV format. Once you are done with changes, click on the Save button and you will see that new customers are added to the database.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/display-all-customers-2.png">

