# Dynamic LINQ Expression

In LINQ, expression trees are used to represent structured queries that target sources of data that implement IQueryable<T>. 

 - The LINQ provider implements the IQueryable<T> interface for querying relational data stores. 
 - The C# compiler compiles queries that target such data sources into code that builds an expression tree at runtime. 
 - The query provider can then traverse the expression tree data structure and translate it into a query language appropriate for the data source.

## Dynamic Queries

Expression trees are also used in LINQ to represent lambda expressions that are assigned to variables of type Expression<TDelegate>.

 - Dynamic queries are useful when the specifics of a query are not known at compile time. 
 - For example, an application might provide a user interface that enables the end user to specify one or more predicates to filter the data. 
 - In order to use LINQ for querying, this kind of application must use expression trees to create the LINQ query at runtime.

We have a list of customers and we will first use LINQ query and then construct the same query using Expression Tree.

```charp
string[] customers = { "Maria Anders", "Ana Trujillo", "Thomas Hardy", "Janine Labrune",
                   "Sven Ottlieb", "Diego Roel", "Eduardo Saavedra", "Patricia McKenna"};
```

Here is the LINQ query.

```charp
var list = customers
    .Where(customer => (customer.ToLower() == "diego roel" || customer.Length <= 12))
    .OrderBy(customer => customer);
```

In the following example shows you how to use expression trees to construct a query against an IQueryable data source and then execute it.

```csharp
// The IQueryable data to query.  
IQueryable<String> queryableData = customers.AsQueryable<string>();

// Compose the expression tree that represents the parameter to the predicate.  
ParameterExpression pe = Expression.Parameter(typeof(string), "customer");

// Create an expression tree that represents the expression 'customer.ToLower() == "diego roel"'.  
Expression left = Expression.Call(pe, typeof(string).GetMethod("ToLower", System.Type.EmptyTypes));
Expression right = Expression.Constant("diego roel");
Expression e1 = Expression.Equal(left, right);

// Create an expression tree that represents the expression 'customer.Length <= 12'.  
left = Expression.Property(pe, typeof(string).GetProperty("Length"));
right = Expression.Constant(12, typeof(int));
Expression e2 = Expression.LessThanOrEqual(left, right);

// Combine the expression trees to create an expression tree that represents the  
// expression '(customer.ToLower() == "diego roel" || customer.Length <= 12)'.  
Expression predicateBody = Expression.OrElse(e1, e2);

// Create an expression tree that represents the expression  
// 'customers.Where(customer => (customer.ToLower() == "diego roel" || customer.Length <= 12))'  
MethodCallExpression whereCallExpression = Expression.Call(
    typeof(Queryable),
    "Where",
    new Type[] { queryableData.ElementType },
    queryableData.Expression,
    Expression.Lambda<Func<string, bool>>(predicateBody, new ParameterExpression[] { pe }));
// ***** End Where *****  

// ***** OrderBy(customer => customer) *****  
// Create an expression tree that represents the expression  
// 'whereCallExpression.OrderBy(customer => customer)'  
MethodCallExpression orderByCallExpression = Expression.Call(
    typeof(Queryable),
    "OrderBy",
    new Type[] { queryableData.ElementType, queryableData.ElementType },
    whereCallExpression,
    Expression.Lambda<Func<string, string>>(pe, new ParameterExpression[] { pe }));
// ***** End OrderBy *****  

// Create an executable query from the expression tree.  
IQueryable<string> results = queryableData.Provider.CreateQuery<string>(orderByCallExpression);
```
[Try it online](https://dotnetfiddle.net/LrXICy)