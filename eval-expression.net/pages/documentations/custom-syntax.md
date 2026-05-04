---
Title: C# Eval Expression Custom Syntax: Operators, Keywords & Examples
MetaDescription: Master C# Eval Expression custom syntax—DateTime, TimeSpan, recursive lambdas, break keywords, and powerful operators with clear examples.
LastMod: 2026-05-04
---

# Custom Syntax

The C# Eval Expression supports most C# keywords and operators.

On top of that, it introduces additional syntax to make your expressions more flexible, more concise, and easier to write.

These features allow you to go beyond standard C# by simplifying common patterns and unlocking new possibilities.

In this section, you will discover custom expressions, keywords, and operators designed to improve your productivity.

- Expression
   - [Constant DateTime](#constant-datetime)
   - [Constant TimeSpan](#constant-timespan)
   - [Inner Expression](#inner-expression)
   - [Recursive Lambda Expression](#recursive-lambda-expression)
- Keywords
   - [Break All](#break-all)
   - [Break Outer](#break-outer)
- Operators
   - [Array Implicit `..`](#array-implicit)
   - [Exclusive-OR - Alternative `^|`](#exclusive-or-alternative)
   - [Exclusive-OR Assignation - Alternative `^|=`](#exclusive-or-assignation-alternative)
   - [Exponent `^^`](#exponent)
   - [Exponent Assignation `^^=`](#exponent-assignment)
   - [Not Equals - Alternative `<>`](#not-equals-alternative)

## Expression

### Constant DateTime

Allow you to specify a `DateTime` value from a string representation.

The value must be wrapped with `#` to be recognized as a `DateTime`.

```csharp
DateTime result = Eval.Execute<DateTime>("#1981/04/13 10:10#");
```

{% include component-try-it.html href='https://dotnetfiddle.net/9JUf0h' %}

You can use any valid date and time format supported by `DateTime`.

### Constant TimeSpan

Allow you to specify a `TimeSpan` value from a string representation.

The value must be wrapped with `##` to be recognized as a `TimeSpan`.

```csharp
TimeSpan result = Eval.Execute<TimeSpan>("##10:10##");
```

{% include component-try-it.html href='https://dotnetfiddle.net/05H9Ul' %}

You can use any valid time format supported by `TimeSpan`.

### Inner Expression

Allow you to use inner expressions inside another expression.

Inner expressions are wrapped with `{ ... }` and are evaluated before the outer expression.
You can use `return`, but it’s optional when the expression contains a single statement.

```csharp
var result = Eval.Execute("return 1 + { return 2 * 3 } + { 5 * 6 }");

// Result: 1 + 6 + 30 = 37
```

{% include component-try-it.html href='https://dotnetfiddle.net/VGjtEs' %}

### Recursive Lambda Expression

Allow you to create a recursive lambda expression.

You can reference the lambda inside its own body to perform recursive operations.

```csharp
var result = Eval.Execute<int>(@"
	Func<int, int> fib = fib(x) => { 
		return x > 2 ? fib(x - 1) + fib(x - 2) : 1; 
	};
	return fib(X);
", new { X = 5 });

// Result: 5
```

{% include component-try-it.html href='https://dotnetfiddle.net/NasPaE' %}

## Keywords

### Break All

Break all loops (`do`, `for`, `foreach`, `while`) or a `switch` statement.

```csharp
var result = Eval.Execute<int>(@"
int result = 0;

for (int x1 = 0; x1 < 10; x1++)
{
    result += x1;

    for (int x2 = 0; x2 < 10; x2++)
    {
        result += x2;

        for (int x3 = 0; x3 < 10; x3++)
        {
            result += x3;
            break all; // break all loops
        }
    }
}

return result;		
");

// Result: 0 + 0 + 0 = 0
```

{% include component-try-it.html href='https://dotnetfiddle.net/IMXDLf' %}

> This keyword is very useful in a `switch` statement to break the outer loop or the switch itself.

### Break Outer

Break the current loop and its immediate parent loop (`do`, `for`, `foreach`, `while`) or `switch` statement.

```csharp
var result = Eval.Execute<int>(@"
int result = 0;

for (int x1 = 0; x1 < 10; x1++)
{
    result += x1;

    for (int x2 = 0; x2 < 10; x2++)
    {
        result += x2;

        for (int x3 = 0; x3 < 10; x3++)
        {
            result += x3;
            break outer; // break current loop and its immediate parent loop 
        }
    }
}

return result;		
");

// Result: 0 + 1 + 2 + 3 + 4 + 5 + 6 + 7 + 8 + 9 = 45 (all value coming from the `result += x1;`
```

{% include component-try-it.html href='https://dotnetfiddle.net/m9DaeQ' %}

> This keyword is very useful in a `switch` statement to break the outer loop or the switch itself.

## Operators

### Array Implicit `..`

Declare an implicit range that can be used in a collection initializer.

```csharp
var result = Eval.Execute("var list = new List<int>() { 1, 2..100 };");
```

{% include component-try-it.html href='https://dotnetfiddle.net/zqx6rE' %}

The expression `2..100` automatically expands to all values from `2` to `100`.

### Exclusive-OR - Alternative `^|`

The exclusive-OR operator alternative. Works the same as the `^` operator.

```csharp
var result = Eval.Execute("true ^| false");

// Result: true
```

{% include component-try-it.html href='https://dotnetfiddle.net/Re4V1c' %}

Use `^|` and `^|=` when the `^` operator is configured for another purpose.

This alternative syntax is useful when the option [`UseCaretForExponent = true`](/options#usecaretforexponent).

### Exclusive-OR Assignation - Alternative `^|=`

The exclusive-OR assignment operator alternative. Works the same as the `^=` operator.

```csharp
var result = Eval.Execute(@"
	bool b = true;
	b ^|= false;
	return b;
");

// Result: true
```

{% include component-try-it.html href='https://dotnetfiddle.net/V699Kw' %}

Use `^|=` when the `^=` operator is configured for another purpose.

This alternative syntax is useful when the option [`UseCaretForExponent = true`](/options#usecaretforexponent).

### Exponent `^^`

Raise a number to the power of another number.

The result is always a `double`.

```csharp
var result = Eval.Execute("2^^3");

// Result: 8
```

{% include component-try-it.html href='https://dotnetfiddle.net/NHXj9D' %}

Use `^^` to avoid conflicts when the `^` operator is already used for exclusive-OR.

Alternatively, you can use the option [`UseCaretForExponent = true`](/options#usecaretforexponent).

### Exponent Assignment `^^=`

Raise the value of a variable or property to the power of an expression, and assign the result back to the variable or property.

The result is always a `double`.

```csharp
var result = Eval.Execute(@"
	var x = 2;
	x ^^= 3;
	return x;
");

// Result: 8
```

{% include component-try-it.html href='https://dotnetfiddle.net/evvgof' %}

Use `^^=` to avoid conflicts when the `^=` operator is configured for another purpose.

Alternatively, you can use the option [`UseCaretForExponent = true`](/options#usecaretforexponent).

### Not Equals - Alternative `<>`

The inequality operator alternative. Works the same as `!=`.

Returns `false` if the operands are equal; otherwise, returns `true`.

```csharp
var result = Eval.Execute("2 + 2 <> 4");

// Result: false
```

{% include component-try-it.html href='https://dotnetfiddle.net/gfWPkR' %}

## Summary

The C# Eval Expression extends standard C# by adding custom syntax for expressions, keywords, and operators.

These features help you write shorter, clearer, and more powerful expressions without changing how C# works.

* Use custom expressions to simplify complex logic
* Use additional keywords to control execution flow more easily
* Use alternative operators to avoid conflicts and improve readability

All these additions are designed to improve your productivity while keeping a familiar C# experience.