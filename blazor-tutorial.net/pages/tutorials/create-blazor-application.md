---
Name: Create Blazor Application
---

# Create Blazor Application

Blazor is an experimental .NET web framework using C#/Razor and HTML that runs in the browser with WebAssembly. Blazor provides all of the benefits of a client-side web UI framework using .NET on the client and optionally on the server.

Once the environment is set up, you can create the Blazor application from **File > New** menu and choose **Project...** menu item.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/create-first-app1.png" alt="Create first app-1">

In the New Project dialog, select **Web** in the left pane and then choose **ASP.NET Core Web Application** in the middle pane. Specify the project name in the Name field and click OK.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/create-first-app2.png" alt="Create first app-2">

On the **New ASP.NET Core Web Application** dialog, choose the Blazor template and select OK. Once the project is created, build and run the application.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/create-first-app3.png" alt="Create first app-3">

 - The Blazor application runs in the browser, and it has three pages: **Home**, **Counter**, and **Fetch data**. 
 - These pages are implemented by the three Razor files in the Pages folder.
 - Each of these files implements a Blazor component that is compiled and executed on the client-side in the browser.

## Home Page

Home page implementation is in Index.cshtml file.

```csharp
@page "/"

<h1>Hello, world!</h1>

Welcome to your new app.

<SurveyPrompt Title="How is Blazor working for you?" />

```

## Counter Page

Counter page has one button, and each time the button is selected, the counter is incremented without a page refresh. 

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/create-first-app3.png" alt="Create first app-4">

Counter page implementation is in Counter.cshtml file.

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
Normally, this kind of client-side behavior is handled in JavaScript; but here, it is implemented in C# and .NET by the Counter component.

## Fetch Data

Take a look at the implementation of the FetchData component in FetchData.cshtml. 

```csharp
@page "/fetchdata"
@inject HttpClient Http

<h1>Weather forecast</h1>

<p>This component demonstrates fetching data from the server.</p>

@if (forecasts == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <table class="table">
        <thead>
            <tr>
                <th>Date</th>
                <th>Temp. (C)</th>
                <th>Temp. (F)</th>
                <th>Summary</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var forecast in forecasts)
            {
                <tr>
                    <td>@forecast.Date.ToShortDateString()</td>
                    <td>@forecast.TemperatureC</td>
                    <td>@forecast.TemperatureF</td>
                    <td>@forecast.Summary</td>
                </tr>
            }
        </tbody>
    </table>
}

@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

The `@inject` directive is used to inject an HttpClient instance into the component. The FetchData component uses the injected HttpClient to retrieve JSON data from the server when the component is initialized.