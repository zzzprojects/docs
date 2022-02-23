---
Name: Attach Associated Entity
---

## Description

GraphDiff can handle associated entities, when updating an entity graph then associated entities are not changed by GraphDiff.

 - The child entity is not a part of the aggregate. The parent's navigation property will be updated, but changes to the child will not be saved.
 - Only the relation is added, but any changes made to the associated entity will not be updated.
 - Associated entity or a collection of associated entities must be specified in the **AssociatedEntity()** or **AssociatedCollection()** method respectively.

## Add Associated Entity

GraphDiff adds the relation of an associated entity to the parent entity when entity graph is updated.

```csharp
var node = new TestNode { Title = "Parent Node" };

using (var context = new TestDbContext())
{
    context.Nodes.Add(node);
    context.SaveChanges();
}

// Simulate detach
node.OneToOneAssociated = new OneToOneAssociatedModel { Title = "Associated Entity" };
using (var context = new TestDbContext())
{
    // Setup mapping
    context.UpdateGraph(node, map => map
        .AssociatedEntity(p => p.OneToOneAssociated));

    context.SaveChanges();
}
```

Similarly, the relation of a collection of associated entities can also be added to the parent entity by using the **AssociatedCollection()** method.

```csharp
var node = new TestNode { Title = "Parent Node" };

using (var context = new TestDbContext())
{
    context.Nodes.Add(node);
    context.SaveChanges();
}

// Simulate detach
node.OneToManyAssociated = new List<OneToManyAssociatedModel>
{
    new OneToManyAssociatedModel { Title = "Associated Entity 1" },
    new OneToManyAssociatedModel { Title = "Associated Entity 2" },
    new OneToManyAssociatedModel { Title = "Associated Entity 3" }
};
using (var context = new TestDbContext())
{
    // Setup mapping
    context.UpdateGraph(node, map => map
        .AssociatedCollection(p => p.OneToManyAssociated));

    context.SaveChanges();
}
```

If the associated collection has many navigation properties with the same parent entity, such as:

```csharp
public class Child
{
  public Parent A { get; set; }
  public Parent B { get; set; }
  public Parent Inverse { get; set; }
}
```

You can choose which navigation to use; otherwise, the first one found will be used (in this case `A`).

```csharp
context.UpdateGraph(entity, map => map.AssociatedCollection(c => c.Children, x => x.B)
```

## Change Associated Entity

Any change in an associated entity will not be saved with parent's navigation property. 

```csharp
var node = new TestNode
{
    Title = "Parent Node",
    OneToOneAssociated = new OneToOneAssociatedModel
    {
        Title = "Associated Entity"
    }
};

using (var context = new TestDbContext())
{
    context.Nodes.Add(node);
    context.SaveChanges();
} // Simulate detach

node.OneToOneAssociated.Title = "Updated Associated Entity";
using (var context = new TestDbContext())
{
    // Setup mapping
    context.UpdateGraph(node, map => map
        .AssociatedEntity(p => p.OneToOneAssociated));

    context.SaveChanges();
}
```

In the above example, the Title of associated entity is updated, but **GraphDiff** will not save the updated Title when entity graph is updated.
