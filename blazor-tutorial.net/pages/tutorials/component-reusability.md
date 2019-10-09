# Component Reusability

In Blazor, you can easily add and reuse any component in other components by declaring them using HTML element syntax. The markup for using a component looks like an HTML tag where the name of the tag is the component type.

The following markup renders a Counter (counter.cshtml) instance.

```csharp
<Counter />
```

Let's add this markup to the Home (index.cshtml) component.

```csharp
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Title="How is Blazor working for you?" />

<Counter />
```

Now you can see the counter component on home page.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/component-reusability.png">
