# Count Vowels in a String (C# Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# Problem Statement

Given a string, count the total number of **vowels** present in it.

Vowels are:

```text
A, E, I, O, U
a, e, i, o, u
```

---

# Example

### Example 1

```text
Input

Hello

Output

Vowel Count = 2
```

Explanation

```text
e
o
```

---

### Example 2

```text
Input

Education

Output

Vowel Count = 5
```

Explanation

```text
E
u
a
i
o
```

---

# Algorithm

1. Read the input string.
2. Convert the string to lowercase.
3. Initialize a counter variable.
4. Traverse each character using a loop.
5. Check whether the character is:
   - a
   - e
   - i
   - o
   - u
6. If yes, increment the counter.
7. Print the total count.

---

# Dry Run

Input

```text
Hello
```

Convert to lowercase

```text
hello
```

Initially

```text
count = 0
```

### Iteration 1

```text
Character = h

Not a vowel

Count = 0
```

---

### Iteration 2

```text
Character = e

Vowel

Count = 1
```

---

### Iteration 3

```text
Character = l

Not a vowel

Count = 1
```

---

### Iteration 4

```text
Character = l

Not a vowel

Count = 1
```

---

### Iteration 5

```text
Character = o

Vowel

Count = 2
```

Output

```text
Vowel Count = 2
```

---

# Dry Run Table

| Character | Is Vowel | Count |
|-----------|----------|------:|
| h | No | 0 |
| e | Yes | 1 |
| l | No | 1 |
| l | No | 1 |
| o | Yes | 2 |

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string text = "Hello";

        text = text.ToLower();

        int count = 0;

        foreach (char ch in text)
        {
            if (ch == 'a' ||
                ch == 'e' ||
                ch == 'i' ||
                ch == 'o' ||
                ch == 'u')
            {
                count++;
            }
        }

        Console.WriteLine("Vowel Count = " + count);
    }
}
```

---

# Output

```text
Vowel Count = 2
```

---

# How It Works

```text
Hello

↓

Convert to Lowercase

↓

hello

↓

Read each character

↓

Is vowel?

↓

Yes

↓

Count++

↓

Display Count
```

---

# Time Complexity

The loop visits every character exactly once.

```text
Time Complexity = O(n)
```

where

```text
n = Length of the string
```

---

# Space Complexity

Only one integer variable (`count`) is used.

```text
Space Complexity = O(1)
```

---

# Edge Cases

### Case 1

Input

```text
AEIOU
```

Output

```text
5
```

---

### Case 2

Input

```text
bcdfg
```

Output

```text
0
```

---

### Case 3

Input

```text
""
```

Output

```text
0
```

---

### Case 4

Input

```text
aEiOu
```

Output

```text
5
```

---

# Common Mistakes

### Mistake 1

Not converting the string to lowercase.

Wrong

```csharp
if(ch == 'a')
```

This ignores uppercase vowels.

Correct

```csharp
text = text.ToLower();
```

---

### Mistake 2

Using assignment operator.

Wrong

```csharp
if(ch = 'a')
```

Correct

```csharp
if(ch == 'a')
```

---

### Mistake 3

Forgetting one or more vowels.

Always check

```text
a
e
i
o
u
```

---

# Alternative Approach

Using `Contains()`

```csharp
using System;

class Program
{
    static void Main()
    {
        string text = "Hello".ToLower();

        int count = 0;

        foreach (char ch in text)
        {
            if ("aeiou".Contains(ch))
            {
                count++;
            }
        }

        Console.WriteLine(count);
    }
}
```

---

# Comparison

| Approach | Time | Space | Interview |
|----------|------|-------|-----------|
| Character Comparison | O(n) | O(1) | ⭐⭐⭐⭐⭐ |
| `Contains()` | O(n) | O(1) | ⭐⭐⭐⭐ |

> **Interview Tip:** Prefer the **character comparison approach** (`ch == 'a' || ch == 'e' || ...`) because it demonstrates your understanding of conditional logic.

---

# Interview Questions

### Q1. What is the time complexity?

**Answer**

Each character is visited once.

```text
O(n)
```

---

### Q2. What is the space complexity?

**Answer**

Only one counter variable is used.

```text
O(1)
```

---

### Q3. Why convert the string to lowercase?

To avoid checking both uppercase and lowercase vowels separately.

---

### Q4. Can we use `Contains()`?

Yes.

```csharp
if ("aeiou".Contains(ch))
```

However, manual character comparison is usually preferred in interviews.

---

# Interview Answer

> To count vowels in a string, I first convert the string to lowercase. Then I traverse each character using a loop. If the character is one of the five vowels (`a`, `e`, `i`, `o`, `u`), I increment a counter. Since every character is visited exactly once, the algorithm has **O(n)** time complexity and **O(1)** space complexity.

---

# Key Points to Remember

- Vowels are **a, e, i, o, u**.
- Convert the string to lowercase.
- Traverse each character once.
- Increment the counter when a vowel is found.
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`
- **Best Interview Approach:** Loop + Character Comparison.