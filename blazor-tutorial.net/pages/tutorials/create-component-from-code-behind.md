# Create Component from Code Behind

You can create view markup and C# code logic in separate files when creating a Blazor component. 

- Using @inherits directive to tell the Blazor compiler to derive the class generated from the Razor view from class specified with this directive. 
- The class specified with @inherits directive must be inherited from BlazorComponent class and it provides all base functionality for the component.

Let's move the C# code logic from Counter.cshtml file to seperate code behind class. 

```csharp
using Microsoft.AspNetCore.Blazor.Components;

namespace BlazorApplication
{
    public class CounterClass : BlazorComponent
    {
        public int CurrentCount { get; set; }

        public void IncrementCount()
        {
            CurrentCount += 5;
        }
    }
}
```

The Counter.cshtml file will use the properties and methods from the code behind class just by adding  `@inherits CounterClass`.

```csharp
@page "/counter"
@inherits CounterClass

<h1>Counter</h1>

<p>Current count: @CurrentCount</p>

<button class="btn btn-primary" onclick="@IncrementCount">Click me</button>
```

Blazor compiler generates class for all the view pages with the same class name as the page name, so here, a specified base class cannot have the same name as Razor View, otherwise, it would cause a compile-time error.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/create-component-from-code-behind.png" alt="Create component from code behind">

Now when the **Click me** button is pressed, the counter is incremented by 5. 
