# One-way Data Binding

In other frameworks such as Angular, one-way data binding is also known as interpolation. 

 - In one-way binding, we need to pass property or variable name along with `@`, i.e., @Title (here Title is either the property or variable). 
 - In the following example, we have done one-way binding with different variables.

```csharp
@page "/one-way-data-binding"

<!-- Use this button to trigger changes in the source values -->
<button onclick="@ChangeValues">Change values</button>

<p>Counter: @Count</p>

@if (ShowWarning)
{
    <p style="background-color: red; padding: 5px">Warning!</p>
}

<p style="background-color: @Background; color=white; padding: 5px">Notification</p>

<ul>
    @foreach (var number in Numbers)
    {
        <li>@number</li>
    }
</ul>

@functions {
    private int Count { get; set; } = 0;
    private bool ShowWarning { get; set; } = true;
    private string Background { get; set; } = "red";
    private List<int> Numbers { get; set; } = new List<int> { 1, 2, 3 };

    private void ChangeValues()
    {
        Count++;
        ShowWarning = !ShowWarning;
        Background = Background == "red" ? "green" : "red";
        Numbers.Add(Numbers.Max() + 1);
    }
}

```

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/one-way-data-binding.png">