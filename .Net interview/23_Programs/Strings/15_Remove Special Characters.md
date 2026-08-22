# Remove Special Characters (C# Interview Notes)

> **Interview Importance:** ⭐⭐⭐⭐☆

Removing special characters is a common string manipulation interview question. There are multiple ways to solve it. In interviews, the **StringBuilder** approach is preferred because it demonstrates your understanding of character processing and has excellent performance. In production code, **Regular Expressions (Regex)** can provide a shorter and more concise solution.

---

# Problem Statement

Given a string, remove all special characters and keep only:

- Alphabets (`A-Z`, `a-z`)
- Digits (`0-9`)

---

## Example 1

```text
Input

Hello@123!

Output

Hello123
```

---

## Example 2

```text
Input

C# is @Awesome!!

Output

CisAwesome
```

---

## Example 3

```text
Input

abc123

Output

abc123
```

---

# Solution 1 – Using StringBuilder (Recommended for Interviews) ⭐⭐⭐⭐⭐

## Idea

Traverse the string one character at a time.

If the character is:

- Letter
- Digit

Append it to the result.

Ignore everything else.

---

## Algorithm

1. Create an empty `StringBuilder`.
2. Traverse each character.
3. Check using `char.IsLetterOrDigit()`.
4. If true, append it.
5. Return the final string.

---

## Dry Run

Input

```text
Hello@123!
```

| Character | Is Letter/Digit | Result |
|-----------|-----------------|--------|
| H | ✔ | H |
| e | ✔ | He |
| l | ✔ | Hel |
| l | ✔ | Hell |
| o | ✔ | Hello |
| @ | ✘ | Hello |
| 1 | ✔ | Hello1 |
| 2 | ✔ | Hello12 |
| 3 | ✔ | Hello123 |
| ! | ✘ | Hello123 |

Output

```text
Hello123
```

---

## C# Code

```csharp
using System;
using System.Text;

class Program
{
    static string RemoveSpecialCharacters(string text)
    {
        StringBuilder result = new StringBuilder();

        foreach (char ch in text)
        {
            if (char.IsLetterOrDigit(ch))
            {
                result.Append(ch);
            }
        }

        return result.ToString();
    }

    static void Main()
    {
        string text = "Hello@123!";

        Console.WriteLine(RemoveSpecialCharacters(text));
    }
}
```

---

## Code Explanation

### Step 1

```csharp
StringBuilder result = new StringBuilder();
```

Creates an empty string builder.

---

### Step 2

```csharp
foreach(char ch in text)
```

Reads one character at a time.

Example

```text
H

↓

e

↓

l

↓

l

↓

o

↓

@

↓

1

↓

2

↓

3

↓

!
```

---

### Step 3

```csharp
char.IsLetterOrDigit(ch)
```

Checks whether the character is a letter or digit.

Examples

| Character | Result |
|-----------|--------|
| A | True |
| z | True |
| 5 | True |
| @ | False |
| # | False |
| ! | False |

---

### Step 4

```csharp
result.Append(ch);
```

Adds only valid characters.

---

### Step 5

```csharp
return result.ToString();
```

Converts the `StringBuilder` into a string.

---

## Time Complexity

```text
O(n)
```

---

## Space Complexity

```text
O(n)
```

---

# Solution 2 – Using Regular Expressions (Regex) ⭐⭐⭐⭐⭐

## Idea

Instead of checking every character manually, use a Regular Expression to remove all characters that are **not** letters or digits.

---

## Regex Pattern

```text
[^a-zA-Z0-9]
```

Explanation

| Pattern | Meaning |
|---------|---------|
| `^` | NOT |
| `a-z` | Lowercase letters |
| `A-Z` | Uppercase letters |
| `0-9` | Digits |

So,

```text
[^a-zA-Z0-9]
```

means

> Match every character that is **NOT** a letter or digit.

---

## Dry Run

Input

```text
Hello@123!
```

Regex finds

```text
@

!
```

Removes them.

Output

```text
Hello123
```

---

## C# Code

```csharp
using System;
using System.Text.RegularExpressions;

class Program
{
    static string RemoveSpecialCharacters(string text)
    {
        return Regex.Replace(text, "[^a-zA-Z0-9]", "");
    }

    static void Main()
    {
        Console.WriteLine(RemoveSpecialCharacters("Hello@123!"));
    }
}
```

---

## Code Explanation

### Step 1

```csharp
Regex.Replace()
```

Searches the string using the given pattern.

---

### Step 2

```csharp
"[^a-zA-Z0-9]"
```

Finds every character that is **not**

- A-Z
- a-z
- 0-9

---

### Step 3

```csharp
""
```

Replace every match with an empty string.

---

### Example

Input

```text
Hello@123!
```

Regex Matches

```text
@

!
```

Replace with

```text
""
```

Result

```text
Hello123
```

---

## Time Complexity

```text
O(n)
```

---

## Space Complexity

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
| Code Length | Longer | Shorter |
| Production Usage | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |

---

# Edge Cases

## Empty String

```text
Input

""

Output

""
```

---

## Only Special Characters

```text
Input

@#$%^

Output

""
```

---

## Only Letters

```text
Input

Hello

Output

Hello
```

---

## Only Numbers

```text
Input

12345

Output

12345
```

---

## Mixed

```text
Input

A@B#1$2

Output

AB12
```

---

# Interview Questions

### Q1. Which solution is preferred in interviews?

The `StringBuilder` approach because it demonstrates character traversal and efficient string construction.

---

### Q2. Which solution is shorter?

The `Regex` solution.

---

### Q3. Which solution is easier to maintain?

For simple filtering, `Regex` is concise. For custom filtering logic, the `StringBuilder` approach is easier to extend and debug.

---

### Q4. What is the time complexity?

Both solutions have:

```text
O(n)
```

---

# Interview Answer

> There are two common ways to remove special characters in C#. The interview-preferred solution uses `StringBuilder` with `char.IsLetterOrDigit()` to iterate through each character and append only letters and digits. Another common production approach uses `Regex.Replace()` with the pattern `[^a-zA-Z0-9]` to remove all non-alphanumeric characters. Both approaches run in **O(n)** time, but the `StringBuilder` solution better demonstrates algorithmic understanding.

---

# Key Points to Remember

- **Interview Preferred:** `StringBuilder` + `char.IsLetterOrDigit()`
- **Production Shortcut:** `Regex.Replace()`
- Both run in **O(n)** time.
- `StringBuilder` is easier to customize.
- `Regex` is concise and powerful for pattern-based string manipulation.