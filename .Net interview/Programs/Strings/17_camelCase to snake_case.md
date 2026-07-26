# CamelCase to snake_case (C# Interview Notes)

> **Interview Importance:** ⭐⭐⭐⭐☆

Converting **camelCase** to **snake_case** is a common string manipulation interview question. It tests your understanding of character traversal, uppercase detection, and efficient string construction.

---

# Problem Statement

Given a string in **camelCase**, convert it to **snake_case**.

### Rules

- Every uppercase letter becomes lowercase.
- Insert an underscore (`_`) before every uppercase letter (except the first character).

---

## Example 1

```text
Input

helloWorld

Output

hello_world
```

---

## Example 2

```text
Input

myFirstProgram

Output

my_first_program
```

---

## Example 3

```text
Input

employeeSalaryDetails

Output

employee_salary_details
```

---

# Solution 1 – Using StringBuilder (Recommended for Interviews) ⭐⭐⭐⭐⭐

## Idea

Traverse the string one character at a time.

- If the character is uppercase
  - Add `_`
  - Convert it to lowercase
- Otherwise
  - Append it directly

---

# Visualization

Input

```text
helloWorld
```

```
h e l l o W o r l d
          ↑
     Uppercase
```

Output

```text
hello_world
```

---

# Algorithm

1. Create an empty `StringBuilder`.
2. Traverse each character.
3. If the character is uppercase
   - Append `_`
   - Append lowercase character.
4. Otherwise append the character.
5. Return the result.

---

# Dry Run

Input

```text
helloWorld
```

| Character | Uppercase? | Result |
|-----------|------------|--------|
| h | No | h |
| e | No | he |
| l | No | hel |
| l | No | hell |
| o | No | hello |
| W | Yes | hello_w |
| o | No | hello_wo |
| r | No | hello_wor |
| l | No | hello_worl |
| d | No | hello_world |

---

# C# Code

```csharp
using System;
using System.Text;

class Program
{
    static string CamelToSnake(string text)
    {
        StringBuilder sb = new StringBuilder();

        foreach (char ch in text)
        {
            if (char.IsUpper(ch))
            {
                sb.Append('_');
                sb.Append(char.ToLower(ch));
            }
            else
            {
                sb.Append(ch);
            }
        }

        return sb.ToString();
    }

    static void Main()
    {
        Console.WriteLine(CamelToSnake("helloWorld"));
    }
}
```

---

# Code Explanation

## Step 1

```csharp
StringBuilder sb = new StringBuilder();
```

Creates an empty result.

---

## Step 2

```csharp
foreach(char ch in text)
```

Reads one character at a time.

Example

```
h

↓

e

↓

l

↓

l

↓

o

↓

W

↓

o

↓

r

↓

l

↓

d
```

---

## Step 3

```csharp
char.IsUpper(ch)
```

Checks whether the current character is uppercase.

Examples

| Character | Result |
|-----------|--------|
| A | True |
| W | True |
| a | False |
| d | False |

---

## Step 4

If uppercase

```csharp
sb.Append('_');
sb.Append(char.ToLower(ch));
```

Example

```
W

↓

_

↓

w
```

Result

```
hello_w
```

---

## Step 5

Otherwise

```csharp
sb.Append(ch);
```

Append the character directly.

---

## Step 6

```csharp
return sb.ToString();
```

Convert `StringBuilder` into a string.

---

# Time Complexity

```text
O(n)
```

---

# Space Complexity

```text
O(n)
```

---

# Solution 2 – Using Regular Expressions (Regex) ⭐⭐⭐⭐☆

## Idea

Find every uppercase letter and replace it with:

```
_lowercaseLetter
```

---

## C# Code

```csharp
using System;
using System.Text.RegularExpressions;

class Program
{
    static string CamelToSnake(string text)
    {
        return Regex.Replace(text, "([A-Z])", "_$1").ToLower();
    }

    static void Main()
    {
        Console.WriteLine(CamelToSnake("helloWorld"));
    }
}
```

---

# How Regex Works

Regex Pattern

```
([A-Z])
```

Meaning

| Pattern | Meaning |
|---------|---------|
| A-Z | Any uppercase letter |
| () | Capture the letter |

Replacement

```
_$1
```

Means

```
_

+

Captured Letter
```

Example

```
helloWorld

↓

hello_World

↓

ToLower()

↓

hello_world
```

---

# Time Complexity

```text
O(n)
```

---

# Space Complexity

```text
O(n)
```

---

# Comparison

| Feature | StringBuilder | Regex |
|---------|---------------|--------|
| Interview Friendly | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Easy to Understand | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Performance | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |
| Code Length | Medium | Very Short |

---

# Edge Cases

### Empty String

```text
Input

""

Output

""
```

---

### Single Word

```text
Input

hello

Output

hello
```

---

### Multiple Uppercase Letters

```text
Input

myHTTPServer

Output

my_h_t_t_p_server
```

> **Note:** This simple solution treats each uppercase letter separately. If you want `my_http_server`, you'll need a more advanced algorithm.

---

# Common Mistakes

## Mistake 1

Forgetting to convert uppercase to lowercase.

Wrong Output

```text
hello_World
```

Correct

```text
hello_world
```

---

## Mistake 2

Using string concatenation (`+=`) inside the loop instead of `StringBuilder`.

---

## Mistake 3

Adding an underscore before the first character.

Wrong

```text
_Hello
```

Correct

```text
hello
```

---

# Interview Questions

### Q1. Which approach is preferred in interviews?

The `StringBuilder` approach because it demonstrates character traversal and efficient string manipulation.

---

### Q2. Which approach is shorter?

The `Regex` approach.

---

### Q3. What is the time complexity?

```
O(n)
```

---

### Q4. Why use `StringBuilder`?

Because strings are immutable in C#. `StringBuilder` avoids creating a new string on every append.

---

# Interview Answer

> To convert camelCase to snake_case, iterate through the string character by character. Whenever an uppercase character is found, append an underscore followed by its lowercase equivalent. Otherwise, append the character as it is. Using `StringBuilder` gives an efficient **O(n)** solution. A shorter production alternative is to use `Regex.Replace()`.

---

# Key Points to Remember

- **Interview Preferred:** `StringBuilder`
- **Production Shortcut:** `Regex`
- Detect uppercase using `char.IsUpper()`
- Convert using `char.ToLower()`
- **Time Complexity:** O(n)
- **Space Complexity:** O(n)