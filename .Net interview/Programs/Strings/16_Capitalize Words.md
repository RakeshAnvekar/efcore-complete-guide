# Capitalize Words (C# Interview Notes)

> **Interview Importance:** ⭐⭐⭐⭐☆

Capitalizing words is a common string manipulation interview question. It tests your understanding of string traversal, character manipulation, and built-in .NET methods.

---

# Problem Statement

Given a string, capitalize the first letter of every word.

---

## Example 1

```text
Input

hello world

Output

Hello World
```

---

## Example 2

```text
Input

welcome to c sharp

Output

Welcome To C Sharp
```

---

## Example 3

```text
Input

i love dotnet

Output

I Love Dotnet
```

---

# Solution 1 – Using String.Split() (Recommended for Interviews) ⭐⭐⭐⭐⭐

## Idea

1. Split the sentence into words.
2. Capitalize the first letter of each word.
3. Join all the words back together.

---

## Algorithm

1. Split the string using space.
2. Traverse each word.
3. Convert the first letter to uppercase.
4. Convert the remaining letters to lowercase.
5. Join all words.

---

## Dry Run

Input

```text
hello world
```

After Split

```text
["hello", "world"]
```

Process

| Word | Capitalized |
|------|-------------|
| hello | Hello |
| world | World |

Output

```text
Hello World
```

---

## C# Code

```csharp
using System;

class Program
{
    static string CapitalizeWords(string sentence)
    {
        string[] words = sentence.Split(' ');

        for (int i = 0; i < words.Length; i++)
        {
            if (words[i].Length > 0)
            {
                words[i] = char.ToUpper(words[i][0]) +
                           words[i].Substring(1).ToLower();
            }
        }

        return string.Join(" ", words);
    }

    static void Main()
    {
        string input = "hello world";

        Console.WriteLine(CapitalizeWords(input));
    }
}
```

---

## Code Explanation

### Split the sentence

```csharp
string[] words = sentence.Split(' ');
```

Example

```text
hello world

↓

["hello", "world"]
```

---

### Traverse each word

```csharp
for(int i = 0; i < words.Length; i++)
```

Visits every word.

---

### Convert first character

```csharp
char.ToUpper(words[i][0])
```

Example

```text
hello

↓

H
```

---

### Convert remaining letters

```csharp
words[i].Substring(1).ToLower()
```

Example

```text
ELLO

↓

ello
```

---

### Join

```csharp
string.Join(" ", words)
```

Produces

```text
Hello World
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

# Solution 2 – Using TextInfo.ToTitleCase() ⭐⭐⭐⭐⭐

## Idea

.NET provides a built-in method to capitalize each word.

---

## C# Code

```csharp
using System;
using System.Globalization;

class Program
{
    static string CapitalizeWords(string sentence)
    {
        TextInfo textInfo = CultureInfo.CurrentCulture.TextInfo;

        return textInfo.ToTitleCase(sentence.ToLower());
    }

    static void Main()
    {
        Console.WriteLine(CapitalizeWords("hello world"));
    }
}
```

---

## Code Explanation

### Convert to Lowercase

```csharp
sentence.ToLower()
```

Ensures all letters become lowercase first.

Example

```text
hELLo WoRLD

↓

hello world
```

---

### Get TextInfo

```csharp
CultureInfo.CurrentCulture.TextInfo
```

Provides culture-specific text formatting.

---

### Capitalize

```csharp
TextInfo.ToTitleCase()
```

Example

```text
hello world

↓

Hello World
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

| Feature | Split + Loop | ToTitleCase |
|---------|--------------|-------------|
| Interview Friendly | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ |
| Easy to Understand | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Production Usage | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| Code Length | Medium | Very Short |
| Flexibility | High | Medium |

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

## Single Word

```text
Input

hello

Output

Hello
```

---

## Already Capitalized

```text
Input

Hello World

Output

Hello World
```

---

## Mixed Case

```text
Input

hELLo wORLd

Output

Hello World
```

---

# Common Mistakes

### Mistake 1

Not checking for empty words.

Wrong

```csharp
words[i][0]
```

If the word is empty, this throws an exception.

---

### Mistake 2

Using only

```csharp
char.ToUpper(words[i][0])
```

This capitalizes only the first letter but leaves the remaining letters unchanged.

Example

```text
hELLo

↓

HELLo ❌
```

Correct

```text
Hello ✔
```

---

### Mistake 3

Using `ToTitleCase()` without calling `ToLower()` first.

Example

```text
Input

hELLo wORLd

Output

HELLo WORLd ❌
```

Correct

```csharp
textInfo.ToTitleCase(sentence.ToLower())
```

---

# Interview Questions

### Q1. Which solution is preferred in interviews?

The `Split()` + loop approach because it demonstrates string manipulation skills.

---

### Q2. Which solution is shorter?

`TextInfo.ToTitleCase()`.

---

### Q3. Why call `ToLower()` before `ToTitleCase()`?

Because `ToTitleCase()` only capitalizes the first letter and may leave the remaining letters unchanged if they are already uppercase.

---

### Q4. What is the time complexity?

Both solutions run in

```text
O(n)
```

---

# Interview Answer

> The interview-preferred approach is to split the sentence into words, capitalize the first character of each word using `char.ToUpper()`, convert the remaining characters to lowercase, and then join the words back together. .NET also provides `TextInfo.ToTitleCase()` for a concise production-ready solution. Both approaches have **O(n)** time complexity.

---

# Key Points to Remember

- **Interview Preferred:** `Split()` + `char.ToUpper()`
- **Production Shortcut:** `TextInfo.ToTitleCase()`
- Use `ToLower()` before `ToTitleCase()`.
- **Time Complexity:** O(n)
- **Space Complexity:** O(n)
```