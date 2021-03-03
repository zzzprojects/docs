# Invoke JavaScript Functions

Blazor is a frontend framework, but it has no direct access to the browser’s DOM API. 
  
 - As web developers, we want to access browser APIs and existing JavaScript functions.
 - There are times when Blazor .NET code is required to call a JavaScript function. 
 - For example, a JavaScript call can expose browser capabilities or functionality from a JavaScript library to the Blazor app.

## JavaScript Interop

A Blazor app can invoke JavaScript functions from .NET and .NET methods from JavaScript code. This property of calling a JS method from C# code and vice versa is referred to as JavaScript Interop. 

 - Blazor uses JavaScript Interop to handle DOM manipulation and browser API calls.
 - To call a JavaScript from .NET, use the `IJSRuntime` abstraction, which is accessible from `JSRuntime.Current`. 

Let's add two JavaScript functions to `index.html` file.

```csharp
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width">
    <title>BlazorApplication</title>
    <base href="/" />
    <link href="css/bootstrap/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
</head>
<body>
    <app>Loading...</app>

    <script src="_framework/blazor.webassembly.js"></script>
    <script type="blazor-boot">
    </script>
    <script>
       function JSMethod() {
            $("#demop").text("JavaScript function called from C#");
        }
    </script>
    <script>
        function CSMethod() {
            DotNet.invokeMethodAsync('BlazorApplication', 'CSCallBackMethod');
        }
    </script>
</body>
</html>
```

 - The `JSMethod` function will set the text of a `<p>` tag having id "demop" to "JavaScript function called from C#".
 - This `CSMethod` function will have a call back to our C# method `CSCallBackMethod` which we will define later.

Now create a new Blazor component `JSInterop.cshtml` and add the following functions.

```csharp
@functions {
    protected static string message { get; set; }
    
    protected void CallJSMethod()
    {
        JSRuntime.Current.InvokeAsync<bool>("JSMethod");
    }

    protected void CallCSMethod()
    {
        JSRuntime.Current.InvokeAsync<bool>("CSMethod");
    }
    
    [JSInvokable]
    public static void CSCallBackMethod()
    {
        message = "C# function called from JavaScript";
    }
}
```

 - The `CallJSMethod` and `CallCSMethod` will call our JS functions `JSMethod` and `CSMethod` by using `JSRuntime.Current.InvokeAsync` method.
 - The `JSRuntime.Current.InvokeAsync` method can take two parameters; the JS function name and any parameter that needed to be supplied to the JS function. But, we are not passing any parameter to JS function.
 - The `CSCallBackMethod` is a static method, and it will be called from the JavaScript function `CSMethod`. 
 - It is decorated with `[JSInvokable]` attribute. 
 - This method will set the value of a string variable message, which will be displayed on the UI.

Add two buttons to the `JSInterop.cshtml` file will call the `CallJSMethod` and `CallCSMethod` method on the `onclick` event.

```csharp
@page "/jsinterop"
@using BlazorApplication.Pages
@using Microsoft.JSInterop

<h1>JavaScript Interop Demo</h1>

<hr />

<button class="btn btn-primary" onclick="@CallJSMethod">Call JS Method</button>
<button class="btn btn-primary" onclick="@CallCSMethod">Call C# Method</button>
<br />
<p id="demop"></p>
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
        message = "C# function called from JavaScript";
    }
}
```

Click on the buttons to call the JavaScript functions and C# method.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/js-interop.png" alt="JS interop">