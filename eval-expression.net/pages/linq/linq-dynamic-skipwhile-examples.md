---
Name: LINQ Dynamic - SkipWhile
---

# LINQ Dynamic - SkipWhile

## LINQ Dynamic SkipWhile Examples
C# Dynamic LINQ SkipWhile examples using an Expression Evaluator.

- [SkipWhile - Simple](#skipwhile-simple)
- [SkipWhile - Indexed](#skipwhile-indexed)

## SkipWhile - Simple
This C# example uses the LINQ SkipWhile method with a dynamic expression to get the elements of the array starting from the first element divisible by 3.

### LINQ
```csharp
private void uiSkipWhile_Simple_LINQ_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var allButFirst3Numbers = numbers.SkipWhile(n => n % 3 != 0);

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element divisible by 3:");
		foreach (var n in allButFirst3Numbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.Linq, uiResult, sb);
}
```

### LINQ Dynamic
```csharp
private void uiSkipWhile_Simple_LINQ_Dynamic_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var allButFirst3Numbers = numbers.SkipWhile(n => "n % 3 != 0");

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element divisible by 3:");
		foreach (var n in allButFirst3Numbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.LinqDynamic, uiResult, sb);
}
```

### LINQ Execute
```csharp
private void uiSkipWhile_Simple_LINQ_Execute_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var allButFirst3Numbers = numbers.Execute<IEnumerable<int>>("SkipWhile(n => n % 3 != 0)");

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element divisible by 3:");
		foreach (var n in allButFirst3Numbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.LinqExecute, uiResult, sb);
}
```

### Result
```txt
All elements starting from first element divisible by 3:
3
9
8
6
7
2
0
```

## SkipWhile - Indexed
This C# example uses the LINQ Indexed SkipWhile method with a dynamic expression to get the elements of the array starting from the first element less than its position.

### LINQ
```csharp
private void uiSkipWhile_Indexed_LINQ_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var laterNumbers = numbers.SkipWhile((n, index) => n >= index);

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element less than its position:");
		foreach (var n in laterNumbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.Linq, uiResult, sb);
}
```

### LINQ Dynamic
```csharp
private void uiSkipWhile_Indexed_LINQ_Dynamic_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var laterNumbers = numbers.SkipWhile((n, index) => "n >= index");

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element less than its position:");
		foreach (var n in laterNumbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.LinqDynamic, uiResult, sb);
}
```

### LINQ Execute
```csharp
private void uiSkipWhile_Indexed_LINQ_Execute_Click(object sender, EventArgs e)
{
	int[] numbers = {5, 4, 1, 3, 9, 8, 6, 7, 2, 0};

	var laterNumbers = numbers.Execute<IEnumerable<int>>("SkipWhile((n, index) => n >= index)");

	var sb = new StringBuilder();

	sb.AppendLine("All elements starting from first element less than its position:");
		foreach (var n in laterNumbers)
		{
			sb.AppendLine(n.ToString());
		}

	My.Result.Show(My.LinqResultType.LinqExecute, uiResult, sb);
}
```

### Result
```txt
All elements starting from first element less than its position:
1
3
9
8
6
7
2
0
```
