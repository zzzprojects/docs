# Map Inheritance Hierarchies

LINQ to SQL supports single-table mapping, and it means a complete inheritance hierarchy is stored in a single database table. 

 - An entire class hierarchy can be mapped to a single table.
 - The table includes columns for all properties of all classes in the hierarchy.
 - The concrete subclass represented by a particular row is identified by the value of a type discriminator column.

In the following code example, Vehicle is defined as the root class and Car, and Truck classes inherit the Vehicle class.

```csharp
[Table]
[InheritanceMapping(Code = "C", Type = typeof(Car))]
[InheritanceMapping(Code = "T", Type = typeof(Truck))]
[InheritanceMapping(Code = "V", Type = typeof(Vehicle), IsDefault = true)]
public class Vehicle
{
    [Column(IsDiscriminator = true)]
    public string DiscKey;
    [Column(IsPrimaryKey = true, IsDbGenerated = true)]
    public int VehicleId;
    [Column]
    public string CompanyName;
}
public class Car : Vehicle
{
    [Column(CanBeNull = true)]
    public int TrimCode;
    [Column]
    public string ModelName;
}

public class Truck : Vehicle
{
    [Column(CanBeNull = true)]
    public int Tonnage;
    [Column(CanBeNull = true)]
    public int Axles;
}
```

## Steps

You can map the inheritance hierarchy by specifying attributes and attribute properties as shown in the following steps. 

 1. Add the Table attribute to the root class.
 2. Add an InheritanceMapping attribute for each class in the hierarchy structure.
 3. For each InheritanceMappingAttribute attribute, add a Code property add a Type property. 
    - Code property holds a value that appears in the IsDiscriminator column to indicate which class or subclass this row of data belongs to.
    - Type property holds a value that specifies which class or subclass the key value signifies.
 4. Add an IsDefault property on only one of the InheritanceMapping attributes which serve to designate a fallback mapping when the discriminator value from the database table does not match any Code value in the inheritance mappings.
 5. Add an IsDiscriminator property for a Column attribute which signifies that this is the column that holds the Code value.

In the data context class, a table is defined only for Vehicles

```csharp
public class VehicleContext : DataContext
{
    public Table<Vehicle> Vehicles;

    public VehicleContext(string connection) : base(connection) { }
}
```

In the following example, car and truck objects are created, and both are stored in the Vehicles table.

```csharp
using (var db = new VehicleContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=VehicleDB;"))
{
    db.CreateDatabase();
    
    Vehicle car = new Car()
    {
        CompanyName = "Toyota",
        TrimCode = 123,
        ModelName = "Prius",
    };
    
    Vehicle truck = new Truck()
    {
        CompanyName = "Nissan",
        Tonnage = 110,
        Axles = 4
    };
    
    db.Vehicles.InsertOnSubmit(car);
    db.Vehicles.InsertOnSubmit(truck);
    
    db.SubmitChanges();
}
```

You can use `OfType<T>()` method to retrieve any particular type of data.

```csharp
using (var db = new VehicleContext(@"Data Source=(localdb)\ProjectsV13;Initial Catalog=VehicleDB;"))
{
    var trucks = db.Vehicles.OfType<Truck>().ToList();
}
```