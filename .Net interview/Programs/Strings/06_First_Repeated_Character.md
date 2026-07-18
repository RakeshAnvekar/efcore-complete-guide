# First Repeated Character in a String (C# Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# Problem Statement

Given a string, find the **first repeated character**.

A repeated character is a character that appears more than once. Return the character that is repeated **first while traversing the string from left to right**.

---

# Example

### Example 1

```text
Input

adsfgdef

Output

d
```

Explanation

```text
Characters

a d s f g d e f

When scanning:

a → First time
d → First time
s → First time
f → First time
g → First time
d → Already seen

First Repeated Character = d
```

---

### Example 2

```text
Input

hello

Output

l
```

---

### Example 3

```text
Input

abcd

Output

No Repeated Character Found
```

---

# Why Use HashSet?

A `HashSet<char>` stores only **unique characters**.

When a character is encountered again,

```csharp
set.Add(ch)
```

returns

```text
False
```

which means it is the **first repeated character**.

---

# Algorithm

1. Read the input string.
2. Create a `HashSet<char>`.
3. Traverse each character.
4. Try to add the character into the `HashSet`.
5. If `Add()` returns `false`, print the character and stop.
6. If the loop completes, no repeated character exists.

---

# Dry Run

Input

```text
adsfgdef
```

Initially

```text
Set = { }
```

---

### Iteration 1

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

Set

```text
{ a }
```

---

### Iteration 2

Character

```text
d
```

Returns

```text
True
```

Set

```text
{ a, d }
```

---

### Iteration 3

Character

```text
s
```

Returns

```text
True
```

Set

```text
{ a, d, s }
```

---

### Iteration 4

Character

```text
f
```

Returns

```text
True
```

Set

```text
{ a, d, s, f }
```

---

### Iteration 5

Character

```text
g
```

Returns

```text
True
```

Set

```text
{ a, d, s, f, g }
```

---

### Iteration 6

Character

```text
d
```

```csharp
set.Add('d')
```

Returns

```text
False
```

Already exists.

Output

```text
First Repeated Character = d
```

---

# Dry Run Table

| Character | `set.Add(ch)` | HashSet | Output |
|-----------|---------------|----------|--------|
| a | True | {a} | - |
| d | True | {a,d} | - |
| s | True | {a,d,s} | - |
| f | True | {a,d,s,f} | - |
| g | True | {a,d,s,f,g} | - |
| d | False | {a,d,s,f,g} | **d** |

---

# C# Program

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        string str = "adsfgdef";

        HashSet<char> set = new HashSet<char>();

        foreach (char ch in str)
        {
            if (!set.Add(ch))
            {
                Console.WriteLine("First Repeated Character = " + ch);
                return;
            }
        }

        Console.WriteLine("No Repeated Character Found");
    }
}
```

---

# Output

```text
First Repeated Character = d
```

---

# How It Works

```text
Read Character

↓

Add to HashSet

↓

Already Exists?

      Yes

       ↓

Print Character

↓

Stop

      No

↓

Continue
```

---

# Time Complexity

Each character is visited once.

HashSet insertion takes **O(1)** average time.

```text
Time Complexity = O(n)
```

---

# Space Complexity

The HashSet stores unique characters.

```text
Space Complexity = O(n)
```

---

# Edge Cases

### Case 1

Input

```text
abcd
```

Output

```text
No Repeated Character Found
```

---

### Case 2

Input

```text
aabbcc
```

Output

```text
a
```

---

### Case 3

Input

```text
hello
```

Output

```text
l
```

---

### Case 4

Input

```text
aaaa
```

Output

```text
a
```

---

# Common Mistakes

### Mistake 1

Using

```csharp
if (set.Add(ch))
```

This finds unique characters, not repeated ones.

Correct

```csharp
if (!set.Add(ch))
```

---

### Mistake 2

Continuing the loop after finding a repeated character.

Use

```csharp
return;
```

or

```csharp
break;
```

to stop immediately.

---

### Mistake 3

Using a `List` instead of a `HashSet`.

A `HashSet` provides much faster lookups.

---

# Interview Questions

### Q1. Why use `HashSet`?

Because it provides fast lookup and automatically detects duplicates.

---

### Q2. What does `set.Add(ch)` return?

- `true` → Character added successfully.
- `false` → Character already exists.

---

### Q3. Why use `!set.Add(ch)`?

Because `Add()` returns `false` when the character is already present, indicating the first repeated character.

---

### Q4. What is the time complexity?

```text
O(n)
```

---

### Q5. What is the space complexity?

```text
O(n)
```

---

# Interview Answer

> To find the first repeated character, I use a `HashSet<char>`. As I traverse the string, I attempt to add each character to the `HashSet`. If `Add()` returns `false`, it means the character has already been seen, so it is the first repeated character. This solution runs in **O(n)** time with **O(n)** space.

---

# Key Points to Remember

- Use `HashSet<char>` to track seen characters.
- `set.Add(ch)` returns:
  - `true` → New character.
  - `false` → Duplicate character.
- The first time `Add()` returns `false`, you've found the first repeated character.
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(n)`
- **Best Interview Approach:** `HashSet<char>` + `!set.Add(ch)`.