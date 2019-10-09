# Component Parameters

In Blazor, you can add parameters to any component which are defined using non-public properties on the component class by decorating that property with `[Parameter]` attribute. 

```csharp
public class CounterClass : BlazorComponent
{
    public int CurrentCount { get; set; }

    [Parameter]
    protected string SubTitle { get; set; }

    public void IncrementCount()
    {
        CurrentCount += 5;
    }
}
```
In markup, you can specify arguments (parameters) for a component using attributes. In the following example, the Home Component (Index.cshtml) sets the value of the SubTitle property of the Counter Component (Counter.cshtml):

```csharp
<Counter SubTitle="Subtitle from Index (Home) page"/>
```

Let's add this markup to the Home (index.cshtml) component.

```csharp
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Title="How is Blazor working for you?" />

<Counter SubTitle="Subtitle from Index (Home) page"/>
```

Now, you can see the subtitle of a counter component on a home page.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/component-parameters.png">
