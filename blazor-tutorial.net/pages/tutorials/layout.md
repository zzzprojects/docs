# Layout

Typically, modern web applications contain more than one page with certain layout elements such as menus, logos, copyright messages, etc. which are present on all pages. 

 - Reusing the code of these layout elements onto all pages would not be a good solution. 
 - It becomes hard to maintain and probably inconsistent over time. 
 - Blazor Layouts solve this problem. Every part of the application in Blazor is a component, so Layout is also considered as a component in Blazor applications.

## Blazor Layout

A Blazor layout is just another Blazor component which is also defined in a Razor template or in C# code, it can contain everything just like Blazor component such as data binding, dependency injection is supported, etc. but Blazor layout includes two additional aspects:

### Inherit from BlazorLayoutComponent

 - The layout component inherits from `BlazorLayoutComponent`. 
 - It adds a property Body to the component which contains the content to be rendered inside the layout.

### Body Keyword
 
 - The layout component contains the Razor keyword `@Body`. 
 - During rendering, it is replaced by the content of the layout.

The default template of Blazor contains MainLayout.cshtml under the shared folder. It works as Layout page.

```csharp
@inherits BlazorLayoutComponent

<div class="sidebar">
    <NavMenu />
</div>

<div class="main">
    <div class="top-row px-4">
        <a href="http://blazor.net" target="_blank" class="ml-md-auto">About</a>
    </div>

    <div class="content px-4">
        @Body
    </div>
</div>

```

The above code sample shows the Razor template of a layout component. 

## Load Blazor Component

To load any Blazor component into a layout, you will need to use `@layout` directive. 

```csharp
@layout MyLayout

@page "/my-data"

<h2>My Data</h2>
...
```

 - When the component is requested its content is loaded into the layout component at the point the `@Body` tag is defined.
 - The compiler converts this directive into a LayoutAttribute, which is applied to the component class.

If you are writing a component in pure C# and want to to use a layout component. Then you can decorate it with the [LayoutAttribute]

```csharp
// Using pure C#

[Layout(typeof(MyLayout))]
public class MyClass : BlazorComponent
{
    ...
}
```

This is ultimately what the compiler will convert the @layout directive to anyway.

## Global/Centralized Layout

In ASP.net MVC, the `_ViewImports.cshtml` file was introduced to provide a mechanism to make directives available to Razor pages globally without adding them to each page. 

 - You can also use this file to define Layout page in Blazor. 
 - Layout is automatically applied to all the Blazor pages in the folder hierarchy if you Layout page in this file.

The default `_ViewImports.cshtml` file looks like this when a new Blazor application is created.

```csharp
@layout MainLayout
```

## Nested Layout

In Blazor, you can also use nested layouts; i.e., Blazor component can reference a layout which references another layout.

 - Nesting layouts can be used to reflect a multi-level menu structure.

Here is the `MyLayout.cshtml`.

```csharp
@inherits BlazorLayoutComponent

<div class="main">
    <div class="top-row px-4">
        <h3>My Layout</h3>
    </div>

    <div class="content px-4">
        @Body
    </div>
</div>
```

Here is the `CustomLayout.cshtml`, which is referencing `MyLayout.cshtml`. 

```csharp
@layout MyLayout
@inherits BlazorLayoutComponent

<div class="main">
    <div class="row">
        <div class="col-sm-12">
            <h2>Nested Layout Example</h2>
        </div>
    </div>
    <div class="row">
        <div class="col-sm-12">
            @Body
        </div>
    </div>
</div>
```

Here is the Nestedlayout.cshtml which is referencing `CustomLayout.cshtml`.

```csharp
@layout CustomLayout
@page "/nestedlayout"

<h3>@Title</h3>

@functions {
string Title = "This is child page using Nested Layout";
}  
```

Layout page reduces duplicate code in your application and make the look and feel consistent throughout the application. 
