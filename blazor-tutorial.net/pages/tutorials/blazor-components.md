---
Name: Blazor Components
---

# Blazor Components

## What is a Component

A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form. Blazor applications are created using components which are flexible, lightweight, and can be nested, reused, and shared between projects.

 - A component is the base element of the Blazor application, i.e., every page is considered as a component in Blazor. 
 - It uses the combination of Razor, HTML and C# code as a component.

## Component Class

A component in Blazor ends up as a class including both the HTML markup to render along with the processing logic needed to inject data or respond to UI events.

```csharp
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

@functions {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```
 - Blazor components are implemented in *.cshtml files using a combination of C# and HTML markup. 
 - The UI for a component is defined using HTML while dynamic rendering logic like loops, conditionals, expressions are added using an embedded C# syntax called Razor.
 - In function block, we can define all the properties that are used in view markup, and the methods are bound with control as an event.
 - When a Blazor app is compiled, the HTML markup and C# rendering logic are converted into a component class, and the name of the generated class matches the name of the file.

## Component Members

Members of the component class are defined in a `@functions` block, and you can use more than one `@functions` block in a component. 

 - In the `@functions` block, component state such as properties and fields are specified along with methods for event handling or for defining other component logics.
 - Component members can then be used as part of the component's rendering logic using C# expressions that start with @. 
