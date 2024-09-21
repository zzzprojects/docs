---
Title: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
MetaDescription: Master Easy Techniques to Propagate Identity and Key Values Across Your Graph 
LastMod: 2024-09-21
---

# Master Easy Techniques to Propagate Identity and Key Values Across Your Graph

Propagating the key or identity value is often necessary when dealing with a graph scenario, such as an Order and Order Items. When you save the Order, you need to propagate the `OrderID` value to all items.

In Dapper Plus, there are four major ways to propagate the key or identity:

- **Auto Identity Propagation**
- **BeforeAction**
- **AfterAction**
- **ThenForEach**

## Auto Identity Propagation

Auto identity propagation is one of the easiest methods. You simply specify during the mapping that the identity value should be automatically propagated (set the second parameter of the `Identity` method to `true`).

```csharp
DapperPlusManager.Entity<Order>()
	.Identity(x => x.OrderID, true);		
DapperPlusManager.Entity<OrderItem>()
	.Identity(x => x.OrderItemID, true);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders).ThenBulkInsert(x => x.Items);
```
[Try it](https://dotnetfiddle.net/nyBt0T)

However, some restrictions apply. The identity must follow one of these conventions (case insensitive):

- The parent and child share the same property name. In our case, `OrderID`.
- The parent has the name `ID` and the child has the property name `[EntityType]ID`. For example, `OrderID`.

## BeforeAction Event

The `BeforeAction` event occurs before an operation is performed. This event is crucial for setting values such as `CreatedDate` and `UpdatedDate`, but it can also be used to propagate key or identity values.

In the following example, we will check if the `OrderID` has been set, and if not, we will obtain the value from the parent.

```csharp
DapperPlusManager.Entity<Order>().Identity(x => x.OrderID);		
DapperPlusManager.Entity<OrderItem>().Identity(x => x.OrderItemID)
	.BeforeAction((actionKind, orderItem) => {
		if (actionKind == DapperPlusActionKind.Insert || actionKind == DapperPlusActionKind.Merge)
		{
			if(orderItem.Parent != null)
			{
				orderItem.OrderID = orderItem.Parent.OrderID;
			}
		}
	});
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders).ThenBulkInsert(x => x.Items);
```

[Try it](https://dotnetfiddle.net/NgQ0wE)

## AfterAction Event

The `AfterAction` event is more straightforward than the `BeforeAction` event. This time, the event is raised after the Order is saved, allowing us to directly propagate the value to all items.

```csharp
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

## ThenForEach Method

The `ThenForEach` method allows you to execute a custom action after a bulk operation has been completed. It acts exactly like the `AfterAction` event, but instead of occurring during the mapping, it is executed through our action chaining, as we have seen in our [Bulk Extensions Method - Chaining](/bulk-extensions-methods#chaining).

```csharp
DapperPlusManager.Entity<Order>().Identity(x => x.OrderID);
DapperPlusManager.Entity<OrderItem>().Identity(x => x.OrderItemID);
	
var connection = new SqlConnection(FiddleHelper.GetConnectionStringSqlServer());

connection.BulkInsert(newOrders)
	.ThenForEach(x => x.Items?.ForEach(y => y.OrderID = x.OrderID))
	.ThenBulkInsert(x => x.Items);
```
[Try it](https://dotnetfiddle.net/UoewoB)

## Conclusion

In this article, we learned how the key/identity value can be implicitly or explicitly propagated. There is no recommended technique to use, as the best choice often depends on your specific scenario.

Still, my favorite method is usually Auto Identity Propagation because it ensures that my property name is consistent throughout all my graph, and there is nothing more I have to do besides setting a property to true.