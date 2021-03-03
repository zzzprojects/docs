# Event Binding

Event binding is quite limited in Blazor. Currently, only `onclick` and `onchange` are supported. You can find more details in the [Blazor GitHub](https://github.com/aspnet/Blazor/issues/503).

```csharp
@page "/event-binding"

<h3>Event Binding</h3> <br /> <br />
<button onclick=@ButtonClicked>Event Binding Example</button>  <br /> <br />
<button onclick=@(() => Message = "Inline:Button Clicked")>Inline Event Binding</button> <br /> <br />
<p>Message: @Message</p>

@functions {
    private string Message { get; set; } = "";
    void ButtonClicked()
    {
        Message = "Button Clicked";
    }
}
```

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/event-binding.png" alt="Event binding">

