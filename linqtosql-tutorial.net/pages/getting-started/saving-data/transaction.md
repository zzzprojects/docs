# Transaction

Transactions allow several database operations to be processed atomically.

 - If the transaction is committed, all of the operations are successfully applied to the database.
 - If the transaction is rolled back, none of the operations are applied to the database.

LINQ to SQL supports transactions as a part of its architecture, and there are three types of transactions in LINQ to SQL.

## Implicit Transaction

When you call SubmitChanges(), LINQ to SQL starts a local transaction by default and uses it to execute the generated SQL commands. When all SQL commands have been completed, LINQ to SQL commits the local transaction.

## Explicit Local Transaction

When you start a local transaction, then it is your responsibility to commit or rollback the transaction after successful execution of the transaction. The connection corresponding to the transaction must match the connection used for constructing the DataContext. An exception is thrown if a different connection is used.

## Explicit Distributable Transaction

You can call LINQ to SQL APIs in the scope of an active Transaction. LINQ to SQL detects that the call is within the scope of a transaction and does not create a new transaction. LINQ to SQL also avoids closing the connection in this case. You can perform query and SubmitChanges executions in the context of such a transaction.

The following example starts a local transaction, and insert a new customer to the database and then commits the transaction.

```csharp

using (var db = new CustomerDataContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=CustomerDB;"))
{
    db.Connection.Open();
    using (var transaction = db.Connection.BeginTransaction())
    {
        db.Transaction = transaction;
    
        try
        {
            var customer = new Customer()
            {
                FirstName = "Maria",
                LastName = "Anders",
                Address = "Obere Str. 57",
                Invoices = new EntitySet<Invoice>()
                {
                    new Invoice() { Date = new DateTime(2018,4,25)},
                    new Invoice() { Date = new DateTime(2018,5,1)},
                }
            };
    
            db.Customers.InsertOnSubmit(customer);
    
            db.SubmitChanges();
            transaction.Commit();
        }
        catch (Exception e)
        {
            transaction.Rollback();
        }
    }
}

```