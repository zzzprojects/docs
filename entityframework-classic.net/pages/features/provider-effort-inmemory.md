---
Name: Effort (In Memory) Provider
---

# Effort (In Memory) Provider

## Description
Effort (**E**ntity **F**ramework **F**ake **O**bjectContext **R**ealization **T**ool) is the official `In Memory` provider for Entity Framework Classic. It creates a fake or mock database that allows you to test the Business Logic Layer (BLL) without worrying about your Data Access Layer (DAL).


NuGet: https://www.nuget.org/packages/Z.EntityFramework.Classic.Effort

Official Documentation: https://entityframework-effort.net/

## Installing

To use Effort, you need to create a transient connection and use it for your context:

```csharp
var connection = Effort.DbConnectionFactory.CreateTransient();
var context = new EntityContext(connection);
```

## Examples

```csharp
using System.Collections.Generic;
using System.Data.Common;
using System.Data.Entity;
using System.Linq;
using System.Windows.Forms;
using Effort;

namespace Z.Lab.EFClassic
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();

            var connection = DbConnectionFactory.CreateTransient();

            using (var context = new EntityContext(connection))
            {
                var list = new List<Customer>();
                for (var i = 0; i < 10; i++)
                {
                    list.Add(new Customer {Name = "ZZZ_" + i});
                }

                context.Customers.AddRange(list);
                context.SaveChanges();
            }

            using (var context = new EntityContext(connection))
            {
                var list = context.Customers.Where(x => x.ID > 3).ToList();
            }
        }

        public class EntityContext : DbContext
        {
            public EntityContext(DbConnection connection) : base(connection, false)
            {
            }

            public DbSet<Customer> Customers { get; set; }
        }

        public class Customer
        {
            public int ID { get; set; }
            public string Name { get; set; }
        }
    }
}
```
