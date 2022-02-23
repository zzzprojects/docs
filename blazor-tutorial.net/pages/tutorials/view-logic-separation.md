---
Name: View Logic Separation
---

# View Logic Separation

By default, Blazor templates generate view logic code inside the Razor template using `@functions`. 

 - In the background, Blazor generates a single class containing C# code for generating the tree of view objects and the C# code representing the view logic.
 - Most of the developers dislike mixing view and logic in a single file.

```csharp
@page "/"

<!-- View (HTML) -->

@functions {
    // Logic (C#)
}
```

There are many ways to separate view and logic which we will discuss here in this article.

## Base Class

One way of separating view and logic is to create a base class. 

 - The base class contains all the view logic (C#). 
 - The Blazor component derives from this class and adds the view. 

Let's have a look at an example which contains the view and view logic in single file. 

```charp
@page "/jsinterop"
@using BlazorApplication.Pages
@using Microsoft.JSInterop

<h1>JavaScript Interop Demo</h1>

<hr />

<button class="btn btn-primary" onclick="@CallJSMethod">Call JS Method</button>
<button class="btn btn-primary" onclick="@CallCSMethod">Call C# Method</button>
<br />
<br />
<p id="demop">JavaScript function called from C#</p>
<br />
<p>@message</p>

@functions {
    protected static string message { get; set; }
    
    protected void CallCSMethod()
    {
        JSRuntime.Current.InvokeAsync<bool>("CSMethod");
    }
    
    protected void CallJSMethod()
    {
        JSRuntime.Current.InvokeAsync<bool>("JSMethod");
    }
    
    [JSInvokable]
    public static void CSCallBackMethod()
    {
        message = "C# function called from  JavaScript ";
    }
}
```

Now, to isolate the view from view logic, let's move the C# code to the base class by creating a new Blazor component class and move all the C# code which is defined inside `@functions`.

```csharp
using Microsoft.JSInterop;
using Microsoft.AspNetCore.Blazor.Components;

namespace BlazorApplication.Pages
{
    public class JSDemoModel : BlazorComponent
    {
        protected static string message { get; set; }

        protected void CallCSMethod()
        {
            JSRuntime.Current.InvokeAsync<bool>("CSMethod");
        }

        protected void CallJSMethod()
        {
            JSRuntime.Current.InvokeAsync<bool>("JSMethod");
        }

        [JSInvokable]
        public static void CSCallBackMethod()
        {
            message = "C# function called from JavaScript ";
        }
    }
}
```

The `@inherits` directive can be used to provide Blazor with a code-behind experience that separates view markup from view logic code.

```csharp
@page "/jsinterop"
@using BlazorApplication.Pages
@using Microsoft.JSInterop
@inherits JSDemoModel

<h1>JavaScript Interop Demo</h1>

<hr />

<button class="btn btn-primary" onclick="@CallJSMethod">Call JS Method</button>
<button class="btn btn-primary" onclick="@CallCSMethod">Call C# Method</button>
<br />
<br />
<p id="demop">JavaScript function called from C#</p>
<br />
<p>@message</p>

```

## Dependency Injection

Blazor supports Dependency Injection, and it is one of the technique to isolate logic that is independent of the view, e.g., core business logic, data access logic, etc. Read more about <a href="{{ site.github.url }}/dependency-injection">Dependency Injection in Blazor</a>.

## Partial Classes

Currently, Blazor does not support partial classes for its components ([issue on GitHub](https://github.com/aspnet/Blazor/issues/278)). Therefore, you cannot just create a separate file and use the same class name as the component.

