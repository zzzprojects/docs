---
Title: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
MetaDescription: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
LastMod: 2025-08-19
---

# Identity Key Propagation /n Master Easy Techniques to Propagate Identity and Key Values Across Your Graph

Propagating the key or identity value is often necessary when dealing with a graph scenario, such as an Order and Order Items. When you save the Order, you need to propagate the `OrderID` value to all items.

In Dapper Plus, there are four major ways to propagate the key or identity:

- [ThenForEach](#thenforeach-method)
- [Auto Identity Propagation](#auto-identity-propagation)
- [AfterAction](#afteraction-event)
- [BeforeAction](#beforeaction-event)

## ThenForEach Method

The `ThenForEach` method allows you to execute a custom action after a bulk operation has been completed. It acts exactly like the [AfterAction](#afteraction-event) event, but instead of occurring during the mapping, it is executed through our action chaining, as we have seen in our [Bulk Extensions Method - Chaining](/bulk-extensions-methods#chaining-methods).

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<Order>().Identity(x => x.OrderID);
DapperPlusManager.Entity<OrderItem>().Identity(x => x.OrderItemID);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders)
	.ThenForEach(x => x.Items?.ForEach(y => y.OrderID = x.OrderID))
	.ThenBulkInsert(x => x.Items);
```
[Try it](https://dotnetfiddle.net/UoewoB)

## Auto Identity Propagation

Auto identity propagation is one of the easiest methods. You simply specify during the mapping that the identity value should be automatically propagated (set the second parameter of the `Identity` method to `true`).

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID, true);		
DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID, true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders).ThenBulkInsert(x => x.Items);
```
[Try it](https://dotnetfiddle.net/nyBt0T)

Some very limited restrictions apply when propagating identities automatically. The identity relationship must follow **one of these conventions** (case insensitive):

### 1 - The parent has the property name 'ID', and the child has the property name '[EntityType]ID'

The parent must define a property named `ID`, and the child must have a property named after the parent entity type, followed by `ID`.

**Example:**

```csharp
public class Order
{
    public int ID { get; set; }
}

public class OrderItem
{
    public int OrderID { get; set; }
}
```

### 2 - The parent has the property name '[EntityType]ID' or '[BaseEntityType]ID', and the child has a matching property name

The parent defines a property using its entity type or base entity type followed by `ID`, and the child must have a property with the **same name**.

**Example:**

```csharp
public class Order
{
    public int OrderID { get; set; }
}

public class OrderItem
{
    public int OrderID { get; set; }
}
```

### Important Note:

For all other cases, the library will **not propagate the identity** to ensure data integrity. If you have a non-standard naming convention, you will need to handle identity propagation manually.

## AfterAction Event

The `AfterAction` event is more straightforward than the `BeforeAction` event. This time, the event is raised after the Order is saved, allowing us to directly propagate the value to all items.

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<Order>().Identity(x => x.OrderID)
	.AfterAction((actionKind, order) => {
		if (actionKind == DapperPlusActionKind.Insert || actionKind == DapperPlusActionKind.Merge)
		{
			if(order.Items != null)
			{
				order.Items.ForEach(x => x.OrderID = order.OrderID);
			}
		}
	});
DapperPlusManager.Entity<OrderItem>().Identity(x => x.OrderItemID);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders).ThenBulkInsert(x => x.Items);
```

[Try it](https://dotnetfiddle.net/jQRbkk)

## BeforeAction Event

The `BeforeAction` event occurs before an operation is performed. This event is crucial for setting values such as `CreatedDate` and `UpdatedDate`, but it can also be used to propagate key or identity values.

In the following example, we will check if the `OrderID` has been set, and if not, we will obtain the value from the parent.

```csharp
// @nuget: Z.Dapper.Plus
using Z.Dapper.Plus;

DapperPlusManager.Entity<Order>().Identity(x => x.OrderID);		
DapperPlusManager.Entity<OrderItem>().Identity(x => x.OrderItemID)
	.BeforeAction((actionKind, orderItem) => {
		if (actionKind == DapperPlusActionKind.Insert || actionKind == DapperPlusActionKind.Merge)
		{
			if(orderItem.OrderID == 0 && orderItem.Parent != null)
			{
				orderItem.OrderID = orderItem.Parent.OrderID;
			}
		}
	});
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders).ThenBulkInsert(x => x.Items);
```

[Try it](https://dotnetfiddle.net/NgQ0wE)

## Conclusion

In this article, we learned how the key/identity value can be implicitly or explicitly propagated. There is no recommended technique to use, as the best choice often depends on your specific scenario.

Still, my favorite method is usually Auto Identity Propagation because it ensures that my property name is consistent throughout all my graph, and there is nothing more I have to do besides setting a property to true.