# Count Words (C# Interview Notes)

## Problem Statement

Given a sentence, count the total number of words.

### Example

```text
Input  : "Hi I am Rakesh"
Output : 4

Input  : "Welcome to C# Programming"
Output : 4
```

---

# Approach

Split the sentence into words using spaces and count the number of elements.

---

# Algorithm

1. Read the input string.
2. Split the string using spaces.
3. Remove empty entries (to handle multiple spaces).
4. Count the number of words.
5. Print the count.

---

# Dry Run

```text
String = "Hi I am Rakesh"

Split()

↓

["Hi", "I", "am", "Rakesh"]

↓

Length = 4

↓

Output = 4
```

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string sentence = "Hi I am Rakesh";

        string[] words = sentence.Split(' ', StringSplitOptions.RemoveEmptyEntries);

        Console.WriteLine("Word Count: " + words.Length);
    }
}
```

---

# Time Complexity

Splitting the string scans every character once.

**Time Complexity: O(n)**

where **n** is the length of the string.

---

# Space Complexity

An array of words is created.

**Space Complexity: O(n)**

---

# Interview Questions

### Q1. How do you count words in a string?

Split the string using spaces and count the number of elements.

---

### Q2. Why use `StringSplitOptions.RemoveEmptyEntries`?

It removes empty strings caused by multiple consecutive spaces.

Example

```text
"Hi   I  am"

Without RemoveEmptyEntries

["Hi", "", "", "I", "", "am"]

With RemoveEmptyEntries

["Hi", "I", "am"]
```

---

# Interview Answer

> Split the sentence using spaces with `StringSplitOptions.RemoveEmptyEntries` and count the number of words using the array's `Length` property. This approach runs in **O(n)** time and **O(n)** space.

---

# Key Points

- Use `Split(' ')` to separate words.
- Use `StringSplitOptions.RemoveEmptyEntries` to ignore extra spaces.
- Number of words = `words.Length`.
- **Time Complexity:** **O(n)**
- **Space Complexity:** **O(n)**