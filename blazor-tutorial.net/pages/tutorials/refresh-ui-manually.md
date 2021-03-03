# Refresh UI Manually

By default, Blazor detects a necessary UI refresh automatically in many scenarios like button click etc. However, there are situations in which you want to trigger a UI refresh manually by using the `BlazorComponent.StateHasChanged` method. 

In the following sample, it changes the application's state using a timer.

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

It will start the count down when you click on Start Countdown button.

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/refresh-ui-manually.png" alt="Refresh UI manually">