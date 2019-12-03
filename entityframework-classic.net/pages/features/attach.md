# Attach & AttachRange

## Description
To improve the performance, you can now use `AttachRange` when attaching multiple entities. The performance gain is similar to `Add vs AddRange` (DetectChanges is called only once in `AddRange` and `AttachRange`).

To improve the flexibility, we added an over that let you attach an entity by specifing the `EntityState`.

## Attach

### Example

```csharp
context.Customers.Attach(customer, EntityState.Deleted);
```
Try it: [NET Core](https://dotnetfiddle.net/gh4uQZ) | [NET Framework](https://dotnetfiddle.net/oGrm5U)

## AttachRange

### Example

```csharp
context.Customers.AttachRange(customers, EntityState.Deleted);
```

Try it: [NET Core](https://dotnetfiddle.net/H1KSi7) | [NET Framework](https://dotnetfiddle.net/jmIlp1)
