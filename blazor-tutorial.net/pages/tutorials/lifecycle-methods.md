# Lifecycle Methods

The Blazor application provides different synchronous as well as asynchronous lifecycle methods.

 - OnInitialized
 - OnInitializedAsync
 - OnParametersSet
 - OnParametersSetAsync
 - OnAfterRender
 - OnAfterRenderAsync
 - ShouldRender 
 
## OnInit & OnInitAsync

The synchronous and asynchronous version of the application methods which gets executed when the component gets Initialized. 

 - The `OnInitialized` is called first, then `OnInitializedAsync`. 
 - It is executed when the component is completely loaded. 
 - You can use this method to load data from services because each control in the UI is loaded after this method. 
 - It is executed when the component is ready and when it has received the values from the parent in the render tree.
 - Any asynchronous operations, which require the component to re-render once they complete, should be placed in the `OnInitializedAsync` method.
 
```csharp
@page "/"

<h1>OnInit & OnInitAsync Demo</h1>

@foreach (var item in EventType)
{
    @item <hr />
}

@functions{
    List<string> EventType = new List<string>();

    protected override void OnInitialized()
    {
        EventType.Add("OnInitialized is called");
    }
    protected override async Task OnInitializedAsync()
    { 
        EventType.Add("OnInitializedAsync is called");
        await Task.Delay(1000);          
    }
}
```

## OnParametersSet & OnParametersSetAsync

The synchronous and asynchronous way of setting the parameter when the component receives the parameter from its parent component.

 - The `OnParametersSet` and `OnParametersSetAsync` methods are called when a component is first initialised.
 - After initialisation, `OnParametersSet` and `OnParametersSetAsync` are called each time new or updated parameters are receieved from the parent in the render tree.

 ```csharp
@page "/"

<h1>OnParametersSet & OnParametersSetAsync Demo</h1>

@foreach (var item in EventType)
{
    @item <hr />
}

 @functions{
    List<string> EventType = new List<string>();
 
    protected override void OnParametersSet()
    {
        EventType.Add("OnParameterSet is called");
    }
    protected override async Task OnParametersSetAsync()
    {
        EventType.Add("OnParametersSetAsync is called");
        await Task.Delay(1000);         
    }
}
```

## OnAfterRender & OnAfterRenderAsync

The synchronous and asynchronous version of the application methods to perform the additional steps like initializing the other components.

 - The `OnAfterRender` and `OnAfterRenderAsync` methods are called after each render of the component. 
 - At the point they are called you can expect that all element and component references are populated.
 - It means that if you need to perform an action, such as attaching an event listener, it requires the elements of the component to be rendered in the DOM.  
 - Another great use for these lifecycle methods is for JavaScript library initialization, which requires DOM elements to be in place to work.

 ```csharp
@page "/"

<h1>OnInit & OnInitAsync Demo</h1>

@foreach (var item in EventType)
{
    @item <hr />
}

 @functions{
    List<string> EventType = new List<string>();

    protected override void OnAfterRender()
    {
        EventType.Add("OnAfterRender is called");
    }

    protected override  async Task OnAfterRenderAsync()
    {
        EventType.Add("OnAfterRenderAsync is called");
        await Task.Delay(1000);           
    }
}
```

## ShouldRender

This method returns a boolean value, if returns true, it means refresh the UI, otherwise changes are not sent to UI.
The `ShouldRender` method always does the initial rendering despite its return value.

```csharp
@page "/"

 <h1>ShouldRender Method Demo</h1>
@foreach (var item in EventType){  @item <hr />}
 @functions{
    List<string> EventType = new List<string>();
 
    protected override bool ShouldRender()
    {
        EventType.Add("ShouldRender is called");
        return true;
    }
}
```

## StateHasChanged

This method notifies the component that its state has changed.
 
 - It is called after any lifecycle method has been called. 
 - It can also be invoked manually to trigger a UI re-render.
 - This method looks at the value returned from ShouldRender to decide if a UI re-render should happen. 
 - However, this only happens after the component has been rendered for the first time.
 
```csharp
@page "/refresh-ui-manually"
@using System.Threading;

<h1>@Count</h1>

<button onclick=@StartCountdown>Start Countdown</button>

@functions {
    private int Count { get; set; } = 10;

    void StartCountdown()
    {
        var timer = new Timer(new TimerCallback(_ =>
        {
            if (Count <= 0) return;
            Count--;

            // Note that the following line is necessary because otherwise
            // Blazor would not recognize the state change and not refresh the UI
            this.StateHasChanged();
        }), null, 1000, 1000);
    }
}
```
