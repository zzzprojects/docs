---
Name: Two-way Data Binding
---

# Two-way Data Binding

Blazor also supports two-way data binding by using `bind` attribute. Currently, Blazor supports only the following data types for two-way data binding.

 - string
 - int
 - DateTime
 - Enum
 - bool

If you need other types (e.g. decimal), you need to provide getter and setter from/to a supported type.

The following example demonstrates different two-way binding scenarios. 

```csharp
@page "/two-way-data-binding"

<p>
    Enter your name: <input type="text" bind="@Name" /><br />
    What is your age? <input type="number" bind="@Age" /><br />
    What is your salery? <input type="number" bind="@Salary" /><br />

    What is your birthday (culture-invariant default format)? <input type="text" bind="@Birthday" /><br />
    What is your birthday (German date format)? <input type="text" bind="@Birthday" format-value="dd.MM.yyyy" /><br />

    Are you a manager? <input type="checkbox" bind="@IsManager" /><br />

    <select id="select-box" bind="@TypeOfEmployee">
        <option value=@EmployeeType.Employee>@EmployeeType.Employee.ToString()</option>
        <option value=@EmployeeType.Contractor>@EmployeeType.Contractor.ToString()</option>
        <option value=@EmployeeType.Intern>@EmployeeType.Intern.ToString()</option>
    </select>
</p>

<h2>Hello, @Name!</h2>

<p>You are @Age years old. You are born on the @Birthday. You are @TypeOfEmployee.</p>

@if (IsManager)
{
    <p>You are a manager!</p>
}

    <p>Your salary is $@Salary</p>

@functions {
private enum EmployeeType { Employee, Contractor, Intern };
private EmployeeType TypeOfEmployee { get; set; } = EmployeeType.Employee;

private string Name { get; set; } = "Mark";
private bool IsManager { get; set; } = true;
private static int Age { get; set; } = 26;
public DateTime Birthday { get; set; } = DateTime.Today.AddYears(-Age);

public decimal Salary { get; set; } = (decimal) 2800.5;
}

```

<img src="https://raw.githubusercontent.com/zzzprojects/Blazor-Tutotrial/master/docs/images/two-way-data-binding.png" alt="Two way data binding">

You can find more information about two-way binding on [Blazor Github](https://github.com/aspnet/Blazor/issues/409).
