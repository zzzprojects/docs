---
Name: Regex_IndexOp
---

# Regex_IndexOp

`Regex_IndexOp` returns the position in the original string where the first character of the captured substring is found using the specified matching options.

```csharp
Regex_IndexOp (
    @input NVARCHAR (MAX),
    @pattern NVARCHAR (MAX),
    @options INT
    )
RETURNS INT
```

## Parameters

  - **input**: The input string that contains the text to convert.
  - **pattern**: The regular expression pattern to match.
  - **options**: A bitwise combination of the enumeration values that provide options for matching.

### Options

You can use any of the following options.

| Options               |Integer Value  | Descritpiton  |
|:----------------------|:--------------|:--------------|
|None                   | 0             | Specifies that no options are set. |
|IgnoreCase             | 1             | Specifies case-insensitive matching. |
|Multiline              | 2             | Multiline mode. Changes the meaning of ^ and $ so they match at the beginning and end, respectively, of any line, and not just the beginning and end of the entire string.|
|ExplicitCapture        | 4             | Specifies that the only valid captures are explicitly named or numbered groups of the form (?<name>�). This allows unnamed parentheses to act as noncapturing groups without the syntactic clumsiness of the expression (?:�).|
|Compiled               | 8             | Specifies that the regular expression is compiled to an assembly. This yields faster execution but increases startup time. |
|Singleline             | 16            | Specifies single-line mode. Changes the meaning of the dot (.) so it matches every character (instead of every character except \n).|
|IgnorePatternWhitespace| 32            | Eliminates unescaped whitespace from the pattern and enables comments marked with #. |
|RightToLeft            | 64            | Specifies that the search will be from right to left instead of from left to right. |
|ECMAScript             | 256           | Enables ECMAScript-compliant behavior for the expression. This value can be used only in conjunction with the `IgnoreCase`, `Multiline`, and `Compiled` values. The use of this value with any other values results in an exception.|
|CultureInvariant       | 512           | Specifies that cultural differences in language are ignored.  |

You can also use more than one option by specifying the sum of their integer values. For example, to specify `IgnoreCase` and `Multiline` options, use 3 integer value and pass it as 3rd parameter.

## Returns

The zero-based index position in the original string where the first character of the captured substring is found or -1 if it is not. 

## Example

```csharp
SELECT SQLNET::Regex_IndexOp('An extraordinary day dawns with each new day.', '\be\w*\b', 1)
SELECT SQLNET::Regex_IndexOp('An extraordinary day dawns with each new day.', '\be\w*\b', 3)
```

# Regex_IndexOp4k

It is equivalent to `Regex_IndexOp` except no NVARCHAR(MAX) parameters; it can be used when input data will never be over 4000 characters as this function offers better performance.

```csharp
Regex_IndexOp4k (
    @input NVARCHAR (4000),
    @pattern NVARCHAR (4000),
    @options INT
    )
RETURNS INT
```

## Example

```csharp
SELECT SQLNET::Regex_IndexOp4k('An extraordinary day dawns with each new day.', '\be\w*\b', 1)
SELECT SQLNET::Regex_IndexOp4k('An extraordinary day dawns with each new day.', '\be\w*\b', 3)
```
