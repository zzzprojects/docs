---
PermaID: 1000074
Name: How to Mock Data
---

# How to Mock Data

## How to mock data and write unit tests with Entity Framework?
 

Entity Framework isn't unit test friendly, so how to mock Entity Framework's DbContext for Unit Testing?

### StackOverflow Related Questions

 - [How do I go about unit testing with Entity Framework and Moq?](https://stackoverflow.com/questions/45558470/how-do-i-go-about-unit-testing-with-entity-framework-and-moq)
 - [How are people unit testing with Entity Framework 6, should you bother?](https://stackoverflow.com/questions/22690877/how-are-people-unit-testing-with-entity-framework-6-should-you-bother)

## Answer

 - When writing tests for your application it is often desirable to avoid hitting the real database, because you will face a lot of problems such as creation, perfomance, interdependences etc. 
 - The preferable approach is to mock corresponding database.
 - To create objects through mock, it needs to perform many settings, especially for test scenarios where queries are made and mocking up test data can be a lot of work.

The best approach for unit testing Entity Framework is mocking in memory by using third party libraries such as Entity Framework Effort etc.

### Entity Framework Effort

[Effort](http://entityframework-effort.net/) is a powerful tool that enables a convenient way to create automated tests for Entity Framework based applications.

A simple modification is enough to make Entity Framework use a fake in-memory database.


```csharp
public List<Customer> GetAllCustomers()
{
    using(MyContext context = Effort.ObjectContextFactory.CreateTransient<MyContext>())
    {
        return context.Customers.ToList();
    }
}
```

The term Transient refers to the lifecycle of the underlying in-memory database. [Click here for more information about Entity Framework Effort](http://entityframework-effort.net/overview)

### Moq

Moq is the only mocking library for .NET developed from scratch to take full advantage of .NET Linq expression trees and lambda expressions, which makes it the most productive, type-safe and refactoring-friendly mocking library available.


```csharp
public List<Customer> GetAllCustomers()
{
    var mockSet = new Mock<DbSet<Customer>>();
    mockSet.As<IQueryable<Customer>>().Setup(m => m.Provider).Returns(data.Provider);
    mockSet.As<IQueryable<Customer>>().Setup(m => m.Expression).Returns(data.Expression);
    mockSet.As<IQueryable<Customer>>().Setup(m => m.ElementType).Returns(data.ElementType);
    mockSet.As<IQueryable<Customer>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

    var mockContext = new Mock<MyContext>();
    mockContext.Setup(c => c.Customers).Returns(mockSet.Object);

    var service = new CustomerService(mockContext.Object);
    return service.GetAllCustomers();
}
```

[Click here for more information about Moq](https://github.com/Moq/moq4)

### NSubstitute 

NSubstitute is designed as a friendly substitute for .NET mocking libraries.


```csharp
public List<Customer> GetAllCustomers()
{
    IDbSet<Customer> customerDbSet = Substitute.For<IDbSet<Customer>>();
    customerDbSet.Provider.Returns(customers.Provider);
    customerDbSet.Expression.Returns(customers.Expression);
    customerDbSet.ElementType.Returns(customers.ElementType);
    customerDbSet.GetEnumerator().Returns(customers.GetEnumerator());
    
    IRepositoryContext repositoryContext = Substitute.For<IRepositoryContext>();
    repositoryContext.Customers.Returns(customerDbSet);

    return repositoryContext.Customers.ToList();
}
```

This provides us with an IDbSet<Customer> fake that we can return from an IRepositoryContext fake.

[Click here for more information about NSubstitute](http://nsubstitute.github.io/help/getting-started/)