# Remove Duplicate Characters Using HashSet (C# Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# Problem Statement

Given a string, remove all duplicate characters using **HashSet** while preserving the order of their first occurrence.

---

# Example

### Example 1

```text
Input

banana

Output

ban
```

---

### Example 2

```text
Input

programming

Output

progamin
```

---

### Example 3

```text
Input

hello

Output

helo
```

---

# Why Use HashSet?

A `HashSet<char>` stores **only unique characters**.

When a duplicate character is added, it is automatically ignored.

This makes removing duplicate characters efficient.

---

# Algorithm

1. Read the input string.
2. Create a `HashSet<char>`.
3. Create an empty string called `result`.
4. Traverse each character of the string.
5. Call `set.Add(ch)`.
6. If it returns `true`, append the character to `result`.
7. If it returns `false`, skip the character.
8. Print the final result.

---

# Dry Run

Input

```text
banana
```

Initially

```text
Set = { }

Result = ""
```

---

### Iteration 1

Character

```text
b
```

```csharp
set.Add('b')
```

Returns

```text
True
```

Result

```text
Set = { b }

Result = b
```

---

### Iteration 2

Character

```text
a
```

```csharp
set.Add('a')
```

Returns

```text
True
```

Result

```text
Set = { b, a }

Result = ba
```

---

### Iteration 3

Character

```text
n
```

```csharp
set.Add('n')
```

Returns

```text
True
```

Result

```text
Set = { b, a, n }

Result = ban
```

---

### Iteration 4

Character

```text
a
```

```csharp
set.Add('a')
```

Returns

```text
False
```

Already exists.

Skip.

---

### Iteration 5

Character

```text
n
```

Returns

```text
False
```

Skip.

---

### Iteration 6

Character

```text
a
```

Returns

```text
False
```

Skip.

---

Final Result

```text
ban
```

---

# Dry Run Table

| Character | `set.Add(ch)` | Result |
|-----------|---------------|--------|
| b | True | b |
| a | True | ba |
| n | True | ban |
| a | False | ban |
| n | False | ban |
| a | False | ban |

---

# C# Program

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        string text = "banana";

        HashSet<char> set = new HashSet<char>();

        string result = "";

        foreach (char ch in text)
        {
            if (set.Add(ch))
            {
                result += ch;
            }
        }

        Console.WriteLine("Result = " + result);
    }
}
```

---

# Output

```text
Result = ban
```

---

# How It Works

```text
banana

↓

Read b

↓

Add to HashSet

↓

True

↓

Append to Result

↓

Read a

↓

Add to HashSet

↓

True

↓

Append to Result

↓

Read n

↓

Add to HashSet

↓

True

↓

Append to Result

↓

Read a

↓

Already Exists

↓

False

↓

Skip

↓

Result = ban
```

---

# Why `set.Add(ch)`?

`Add()` performs two operations:

1. Checks whether the character already exists.
2. If not, adds it to the `HashSet`.

It returns:

- `true` → Character added.
- `false` → Character already exists.

That's why we write:

```csharp
if (set.Add(ch))
{
    result += ch;
}
```

Instead of

```csharp
if (!set.Contains(ch))
{
    set.Add(ch);
    result += ch;
}
```

The first approach is shorter and performs the check and insertion in one step.

---

# Time Complexity

Each character is processed once.

HashSet operations (`Add`) take **O(1)** on average.

```text
Time Complexity = O(n)
```

where

```text
n = Length of the string
```

---

# Space Complexity

The `HashSet` stores unique characters.

```text
Space Complexity = O(n)
```

---

# Edge Cases

### Case 1

Input

```text
aaaaaa
```

Output

```text
a
```

---

### Case 2

Input

```text
abcdef
```

Output

```text
abcdef
```

---

### Case 3

Input

```text
""
```

Output

```text
""
```

---

### Case 4

Input

```text
aabbccddeeff
```

Output

```text
abcdef
```

---

# Common Mistakes

### Mistake 1

Appending every character without checking `Add()`.

Wrong

```csharp
result += ch;
```

Correct

```csharp
if (set.Add(ch))
{
    result += ch;
}
```

---

### Mistake 2

Using `HashSet` alone for output.

A `HashSet` stores unique values, but its enumeration order is **not guaranteed**. Build the `result` string while iterating through the original string to preserve the original order.

---

### Mistake 3

Forgetting to import the namespace.

```csharp
using System.Collections.Generic;
```

---

# Interview Questions

### Q1. Why use `HashSet`?

To store only unique characters and remove duplicates efficiently.

---

### Q2. What does `set.Add(ch)` return?

- `true` → Character added.
- `false` → Character already exists.

---

### Q3. What is the time complexity?

```text
O(n)
```

---

### Q4. What is the space complexity?

```text
O(n)
```

---

### Q5. Why not use only `HashSet` for the output?

Because a `HashSet` does **not guarantee element order**. Appending characters to `result` when they are first added preserves the original order.

---

# Interview Answer

> To remove duplicate characters, I use a `HashSet<char>` to track the characters that have already been seen. As I traverse the string, I call `set.Add(ch)`. If it returns `true`, I append the character to the result string. If it returns `false`, I skip it because it is a duplicate. This approach processes each character only once, giving a time complexity of **O(n)** and a space complexity of **O(n)**.

---

# Key Points to Remember

- Use `HashSet<char>` to store unique characters.
- `Add()` returns `true` for a new character and `false` for a duplicate.
- Build the result string while traversing the original string.
- Preserve the original order of characters.
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(n)`
- **Best Interview Approach:** `HashSet<char>` + `set.Add(ch)`.