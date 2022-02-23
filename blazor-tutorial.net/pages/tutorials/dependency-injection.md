---
Name: Dependency Injection
---

# Dependency Injection

Dependency Injection (DI) is a technique for achieving Inversion of Control (IoC) between classes and their dependencies. In other words, it is a technique for accessing services configured in a central location.

 - Blazor has built-in support for dependency injection (DI). 
 - Blazor applications can use built-in services by having them injected into components. 
 - Blazor apps can also define custom services and make them available via DI.

## DI Service Configuration

Blazor's DI system is based on the [DI system in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-2.1). DI services can be configured with the following lifetimes:

|Method	    |Description    |
|:----------|:--------------|
|[Singleton](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.singleton?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_ServiceDescriptor_Singleton__1_System_Func_System_IServiceProvider___0__)	|DI creates a single instance of the service. All components requiring this service receive a reference to this instance.|
|[Transient](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.transient?view=aspnetcore-2.1)	|Whenever a component requires this service, it receives a new instance of the service.|
|[Scoped](https://docs.microsoft.com/en-us/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.scoped?view=aspnetcore-2.1)	    |Blazor doesn't currently have the concept of DI scopes. Scoped behaves like Singleton. Therefore, prefer Singleton and avoid Scoped.|

## Default services

By default, Blazor's `BrowserServiceProvider` automatically adds the following services to the service collection of an application.

|Method	    |Description    |
|:----------|:--------------|
|[IUriHelper](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Services.IUriHelper.html)	|Helpers for working with URIs and navigation state (singleton).|
|[HttpClient](https://docs.microsoft.com/en-us/dotnet/api/system.net.http.httpclient?view=netframework-4.7.2)	|Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton). |

## Add services to DI

Here is a simple service which will retrieve employee's data asynchronously. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;

namespace BlazorApplication.Services
{
    public class Employee
    {
        public string FirstName { get; set; }

        public string LastName { get; set; }
    }

    public interface IRepository
    {
        Task<IReadOnlyList<Employee>> GetAllEmployeesAsync();
    }

    public class Repository : IRepository
    {
        private static Employee[] Employees { get; set; } = new[]
        {
            new Employee { FirstName = "Andy", LastName = "White" },
            new Employee { FirstName = "Mark", LastName = "Doe" }
        };

        public async Task<IReadOnlyList<Employee>> GetAllEmployeesAsync()
        {
            await Task.Delay(100);

            return Repository.Employees;
        }
    }
}

```

When a new Blazor application is created, you will see `Startup.cs` file which contains `ConfigureServices` and `Configure` methods.

```csharp
using Microsoft.AspNetCore.Blazor.Builder;
using Microsoft.Extensions.DependencyInjection;

namespace BlazorApplication
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
        }

        public void Configure(IBlazorApplicationBuilder app)
        {
            app.AddComponent<App>("app");
        }
    }
}

```

The ConfigureServices method has IServiceCollection as an argument, which is a list of service descriptor objects (ServiceDescriptor). You can add services to the service collection by providing service descriptors. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IRepository, Repository>();
}
```

## Injecting Services

To inject a service in a component, use the `@inject` keyword as shown in the following sample. 

```csharp
@using BlazorApplication.Services
@page "/"
@inject IRepository Repository

<h1>Hello, world!</h1>
<ul>
    @if (Employees != null)
    {
        @foreach (var emp in Employees)
         {
             <li>@emp.FirstName @emp.LastName</li>
         }
    }
</ul>

@functions {
    private IReadOnlyList<Employee> Employees;

    protected override async Task OnInitAsync()
    {
        Employees = await Repository.GetAllEmployeesAsync();
    }
}
```

Technically, it generates a property and decorate it with the `InjectAttribute` attribute. 

If a base class is required for Blazor components and injected properties are also required for the base class, then add `InjectAttribute` manually.

```csharp
public class ComponentBase : BlazorComponent
{
    [Inject]
    protected IRepository Repository { get; set; }
    ...
}
```

