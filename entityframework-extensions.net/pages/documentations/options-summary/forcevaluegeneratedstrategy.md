---
Name: ForceValueGeneratedStrategy
LastMod: 2023-10-11
---

# ForceValueGeneratedStrategy Option

## Obsolete Option

This option has been removed in the [v8.103.0.0](https://github.com/zzzprojects/EntityFramework-Extensions/releases/tag/8.103.0.0)

The ForceValueGeneratedStrategy option has been replaced by the [ExplicitValueResolutionMode](https://entityframework-extensions.net/explicit-value-resolution-mode) option.

See the following code to understand how to replace the old options with the new options:

```csharp
// The option `options.ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnAdd` has been replaced by:
options.ExplicitValueResolutionMode = Z.EntityFramework.Extensions.ExplicitValueResolutionMode.AlwaysKeepValueOnInsert;

// The option `options.ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnUpdate` has been replaced by
options.ExplicitValueResolutionMode = Z.EntityFramework.Extensions.ExplicitValueResolutionMode.AlwaysKeepValueOnUpdate;

// The option `options.ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnAddOrUpdate` has been replaced by:
options.ExplicitValueResolutionMode = Z.EntityFramework.Extensions.ExplicitValueResolutionMode.AlwaysKeepValueOnInsertOrUpdate;
```

You can find more about this breaking change in this article: [How EFE Bulk Extensions handle explicit value in EF Core?](https://entityframework-extensions.net/explicit-value-resolution-mode)

## Description

In EF Core, you can set a value to a property even if you have set a [default value](https://www.learnentityframeworkcore.com/configuration/fluent-api/hasdefaultvalue-method) in the database with `.HasDefaultValue(100);`

- **A value is set**: The column will be included in the `INSERT` statement, and the value will be inserted in the database.
- **No value is set**: The property will be ignored and won't be part of the `INSERT` statement (so the default value will be inserted).

Using `SaveChanges` or [BulkSaveChanges](https://entityframework-extensions.net/bulk-savechanges), both follow this behavior and handle this scenario correctly.

For performance optimization, other bulk methods like [EF Core Bulk Insert](https://entityframework-extensions.net/bulk-insert) don't have this behavior. The default behavior always ignore the property value and lets the default value be inserted, even if a value has been specified in your entity.

For example, if the `TokenLimit` has a default value of `100`, both customers will have the value `100` inserted, even if a `TokenLimit` was specified for the **ZZZ Projects** customer.

```csharp
var customers = new List<Customer>();

customers.Add(new Customer() { Name = "ZZZ Projects", TokenLimit = 200 });
customers.Add(new Customer() { Name = "Jonathan Magnan"});

context.BulkInsert(customers);
```

| CustomerID | Name | TokenLimit |
| :--------- | :--- | :--------- |
| 1 | ZZZ Projects | 100 |
| 2 | Jonathan Magnan | 100 |

[Try it](https://dotnetfiddle.net/nXHEtg)

However, you can change this default behavior by using the `ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnAddOrUpdate`, which will now **always** insert the property value (even if none was specified). In this scenario, `200` will be inserted for the **ZZZ Projects** customer, and `0` will be inserted for **Jonathan Magnan** (The `TokenLimit` property had the value `0` for him).

```csharp
var customers = new List<Customer>();

customers.Add(new Customer() { Name = "ZZZ Projects", TokenLimit = 200 });
customers.Add(new Customer() { Name = "Jonathan Magnan"});

context.BulkInsert(customers, options => {
	options.ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnAddOrUpdate;
});
```
| CustomerID | Name | TokenLimit |
| :--------- | :--- | :--------- |
| 1 | ZZZ Projects | 200 |
| 2 | Jonathan Magnan | 0 |

[Try it](https://dotnetfiddle.net/nXHEtg)

## Mixed Scenario

We support either always ignoring the property value or always using it.

Currently, we don't support a mix scenario where you set a value for some entities and not for others. If you come across this case, you'll need to split it into two separate bulk inserts:

```csharp
context.BulkInsert(customers.Where(x => x.TokenLimit == 0));
context.BulkInsert(customers.Where(x => x.TokenLimit != 0), options => {
	options.ForceValueGeneratedStrategy = ValueGeneratedStrategyType.OnAddOrUpdate;
});
```