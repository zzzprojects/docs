---
PermaID: 1000204
Name: dapper-insert-and-update
---

# Dapper - Insert and Update

The `Execute()` extension method in Dapper enables you to execute a command one or multiple times and return the number of affected rows. This method is usually used to execute:

 - INSERT statement
 - UPDATE statement
 - DELETE statement
 - Stored Procedure

## Insert

Inserting a single new record is easy. All we need to do is write an `INSERT` statement with parameters for each column that we want to set. To insert data into the database, the `CustomerController` contains two `Create` action methods.

 - The first method is for the `GET` process which will run when we call the method.
 - The second method is for the `POST` process which will run when we submit the entry to the server.

In Controller `Create` action for `POST`, let's add the following code to insert new customer records to the **Customers** table.

```csharp
// GET: Customer/Create
public ActionResult Create()
{
    return View();
}

// POST: Customer/Create
[HttpPost]
public ActionResult Create(Customer customer)
{
    try
    {
        using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
        {
            string sqlQuery = "Insert Into Customers (FirstName, LastName, Email) Values(@FirstName, @LastName, @Email)";

            int rowsAffected = db.Execute(sqlQuery, customer);
        }

        return RedirectToAction("Index");
    }
    catch
    {
        return View();
    }
}
```

Let's create a view by right-clicking on the `Create` action method in `CustomerController` and select **Add View**. On Add Scaffold dialog, choose **MVC 5 View** and click on the Add button.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-view-4.png">

 - On the Add View dialog, the View name will appear by default. 
 - Select the template from the Template dropdown list for which you want to create a view, e.g. Create.
 - Choose your model class, e.g. Customer
 
Click the Add button and it will add the following code for you.

```csharp
@model DapperDemoApp.Models.Customer

@{
    ViewBag.Title = "Create";
}

<h2>Create</h2>


@using (Html.BeginForm()) 
{
    @Html.AntiForgeryToken()
    
    <div class="form-horizontal">
        <h4>Customer</h4>
        <hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
        <div class="form-group">
            @Html.LabelFor(model => model.FirstName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FirstName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FirstName, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            @Html.LabelFor(model => model.LastName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.LastName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.LastName, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            @Html.LabelFor(model => model.Email, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Email, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Email, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            <div class="col-md-offset-2 col-md-10">
                <input type="submit" value="Create" class="btn btn-default" />
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

[Try it online](https://dotnetfiddle.net/415Svb)

## Update

Updating an existing entity is similar to inserting. All we need is a SQL statement containing an `UPDATE` statement that sets the appropriate columns. We also want to make sure we include a `WHERE` clause limiting the update only to the row with the specified `Id`.

To update data to the database, the `CustomerController` contains two `Edit` action methods.

 - The first method for the `GET` process which will run when we call the method.
 - The second method for the `POST` process which will run when we submit the entry to the server.
 
In Controller `Edit` actions, let's add the following code to update the existing customer record into the **Customers** table.

```csharp
// GET: Customer/Edit/5
public ActionResult Edit(int id)
{
    Customer customer = new Customer();
    using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
    {
        customer = db.Query<Customer>("Select * From Customers WHERE CustomerID =" + id, new { id }).SingleOrDefault();
    }
    return View(customer);
}

// POST: Customer/Edit/5
[HttpPost]
public ActionResult Edit(int id, Customer customer)
{
    try
    {
        using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
        {
            string sqlQuery = "UPDATE Customers set FirstName='" + customer.FirstName + 
                "',LastName='" + customer.LastName + 
                "',Email='" + customer.Email + 
                "' WHERE CustomerID=" + customer.CustomerID;

            int rowsAffected = db.Execute(sqlQuery);
        }

        return RedirectToAction("Index");
    }
    catch
    {
        return View();
    }
}
```

Let's create a view by right-clicking on the `Edit` action method in `CustomerController` and select **Add View**. On Add Scaffold dialog, choose **MVC 5 View** and click on the Add button.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-view-5.png">

 - On the Add View dialog, the View name will appear by default. 
 - Select the template from the Template dropdown list for which you want to create a view, e.g. Edit.
 - Choose your model class, e.g. Customer
 
Click the Add button and it will add the following code for you.

```csharp
@model DapperDemoApp.Models.Customer

@{
    ViewBag.Title = "Edit";
}

<h2>Edit</h2>


@using (Html.BeginForm())
{
    @Html.AntiForgeryToken()
    
    <div class="form-horizontal">
        <h4>Customer</h4>
        <hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
        @Html.HiddenFor(model => model.CustomerID)

        <div class="form-group">
            @Html.LabelFor(model => model.FirstName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.FirstName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.FirstName, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            @Html.LabelFor(model => model.LastName, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.LastName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.LastName, "", new { @class = "text-danger" })
            </div>
        </div>

        <div class="form-group">
            @Html.LabelFor(model => model.Email, htmlAttributes: new { @class = "control-label col-md-2" })
            <div class="col-md-10">
                @Html.EditorFor(model => model.Email, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Email, "", new { @class = "text-danger" })
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

[Try it online](https://dotnetfiddle.net/C1Akcb)

## Delete

Deleting an entity is the easiest of all, because it only requires a unique `Id` to identify the entity being deleted. The SQL statement is a simple `DELETE` with a `WHERE` clause on the `Id` column.

To delete a record from the database, the `CustomerController` contains two `Delete` action methods.

 - The first method is for the `GET` process which will run when we call the method.
 - The second method is for the `POST` process which will run when we submit the delete request to the server.

In Controller `Delete` actions, let's add the following code to delete the existing customer record from the **Customers** table.

```csharp
// GET: Customer/Delete/5
public ActionResult Delete(int id)
{
    Customer customer = new Customer();
    using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
    {
        customer = db.Query<Customer>("Select * From Customers WHERE CustomerID =" + id, new { id }).SingleOrDefault();
    }
    return View(customer);
}

// POST: Customer/Delete/5
[HttpPost]
public ActionResult Delete(int id, FormCollection collection)
{
    try
    {
        using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["CustomerConnection"].ConnectionString))
        {
            string sqlQuery = "Delete From Customers WHERE CustomerID = " + id;

            int rowsAffected = db.Execute(sqlQuery);
        }

        return RedirectToAction("Index");
    }
    catch
    {
        return View();
    }
}
```

Let's create a view by right-clicking on the `Delete` action method in `CustomerController` and select **Add View**. On Add Scaffold dialog, choose **MVC 5 View** and click on the Add button.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-view-6.png">

 - On the Add View dialog, the View name will appear by default. 
 - Select the template from the Template dropdown list for which you want to create a view, e.g. Delete.
 - Choose your model class, e.g. Customer
 
Click the Add button and it will add the following code for you.

```csharp
@model DapperDemoApp.Models.Customer

@{
    ViewBag.Title = "Delete";
}

<h2>Delete</h2>

<h3>Are you sure you want to delete this?</h3>
<div>
    <h4>Customer</h4>
    <hr />
    <dl class="dl-horizontal">
        <dt>
            @Html.DisplayNameFor(model => model.FirstName)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.FirstName)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.LastName)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.LastName)
        </dd>

        <dt>
            @Html.DisplayNameFor(model => model.Email)
        </dt>

        <dd>
            @Html.DisplayFor(model => model.Email)
        </dd>

    </dl>

    @using (Html.BeginForm()) {
        @Html.AntiForgeryToken()

        <div class="form-actions no-color">
            <input type="submit" value="Delete" class="btn btn-default" /> |
            @Html.ActionLink("Back to List", "Index")
        </div>
    }
</div>
```

[Try it online](https://dotnetfiddle.net/JY5ivx)

Let's run your application and you will see all the customers.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/display-all-customers.png">

Now click on Create link and you will see a Create page.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/create-customer.png">

Enter data in all the fields and press Create button, you will the index view.

<img src="https://raw.githubusercontent.com/zzzprojects/docs/master/dapper-tutorial.net/images/display-all-customers-1.png">

Similarly, you can try the Edit link as well to update any customer data.
