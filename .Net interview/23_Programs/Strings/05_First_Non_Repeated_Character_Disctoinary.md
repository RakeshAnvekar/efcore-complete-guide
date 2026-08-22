# First Non-Repeated Character in a String (C# Interview Notes)

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# Problem Statement

Given a string, find the **first character that appears only once**.

If every character is repeated, print an appropriate message.

---

# Example

### Example 1

```text
Input

adsfgdef

Output

a
```

Explanation

```text
Character Frequency

a → 1
d → 2
s → 1
f → 2
g → 1
e → 1
```

The first character with frequency **1** is

```text
a
```

---

### Example 2

```text
Input

aabbcc

Output

No Non-Repeated Character Found
```

---

### Example 3

```text
Input

swiss

Output

w
```

---

# Why Use Dictionary?

A `Dictionary<char, int>` stores

- Character (Key)
- Frequency (Value)

Example

```text
adsfgdef

↓

Dictionary

a → 1
d → 2
s → 1
f → 2
g → 1
e → 1
```

Now it becomes easy to find the first character whose frequency is **1**.

---

# Algorithm

### Step 1

Create a dictionary.

```csharp
Dictionary<char, int> dict = new Dictionary<char, int>();
```

---

### Step 2

Traverse the string and count the frequency of each character.

---

### Step 3

Traverse the string again.

---

### Step 4

If frequency is

```text
1
```

print that character and stop.

---

# Dry Run

Input

```text
adsfgdef
```

Initially

```text
Dictionary = { }
```

---

Read

```text
a
```

Dictionary

```text
a → 1
```

---

Read

```text
d
```

Dictionary

```text
a → 1
d → 1
```

---

Read

```text
s
```

Dictionary

```text
a → 1
d → 1
s → 1
```

---

Read

```text
f
```

Dictionary

```text
a → 1
d → 1
s → 1
f → 1
```

---

Read

```text
g
```

Dictionary

```text
a → 1
d → 1
s → 1
f → 1
g → 1
```

---

Read

```text
d
```

Dictionary

```text
a → 1
d → 2
s → 1
f → 1
g → 1
```

---

Read

```text
e
```

Dictionary

```text
a → 1
d → 2
s → 1
f → 1
g → 1
e → 1
```

---

Read

```text
f
```

Dictionary

```text
a → 1
d → 2
s → 1
f → 2
g → 1
e → 1
```

---

Second Traversal

```text
a → Frequency = 1

First Non-Repeated Character = a
```

---

# Dry Run Table

| Character | Dictionary |
|-----------|------------|
| a | a→1 |
| d | a→1, d→1 |
| s | a→1, d→1, s→1 |
| f | a→1, d→1, s→1, f→1 |
| g | a→1, d→1, s→1, f→1, g→1 |
| d | a→1, d→2, s→1, f→1, g→1 |
| e | a→1, d→2, s→1, f→1, g→1, e→1 |
| f | a→1, d→2, s→1, f→2, g→1, e→1 |

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

        Dictionary<char, int> dict = new Dictionary<char, int>();

        // Count frequency
        foreach (char ch in str)
        {
            if (dict.ContainsKey(ch))
            {
                dict[ch]++;
            }
            else
            {
                dict[ch] = 1;
            }
        }

        // Find first non-repeated character
        foreach (char ch in str)
        {
            if (dict[ch] == 1)
            {
                Console.WriteLine("First Non-Repeated Character = " + ch);
                return;
            }
        }

        Console.WriteLine("No Non-Repeated Character Found");
    }
}
```

---

# Output

```text
First Non-Repeated Character = a
```

---

# How It Works

```text
Read String

↓

Count Frequency

↓

Store in Dictionary

↓

Traverse Again

↓

Frequency == 1 ?

↓

Yes

↓

Print Character

↓

Stop
```

---

# Time Complexity

### First Loop

```text
O(n)
```

### Second Loop

```text
O(n)
```

Overall

```text
O(n)
```

---

# Space Complexity

Dictionary stores the frequency of each unique character.

```text
O(n)
```

---

# Edge Cases

### Case 1

Input

```text
aabbcc
```

Output

```text
No Non-Repeated Character Found
```

---

### Case 2

Input

```text
a
```

Output

```text
a
```

---

### Case 3

Input

```text
abcabcx
```

Output

```text
x
```

---

### Case 4

Input

```text
swiss
```

Output

```text
w
```

---

# Common Mistakes

### Mistake 1

Using `HashSet` only.

A `HashSet` stores unique values but **does not count occurrences**.

Use a `Dictionary<char, int>` for frequency counting.

---

### Mistake 2

Checking the dictionary before counting all characters.

Always complete the first traversal before searching for the answer.

---

### Mistake 3

Returning the first unique key from the dictionary.

The dictionary stores frequencies but does **not guarantee the original string order**. Traverse the original string again to find the **first** non-repeated character.

---

# HashSet vs Dictionary

| Feature | HashSet | Dictionary |
|---------|----------|------------|
|Stores Unique Values|✅|❌|
|Stores Frequency|❌|✅|
|Best for Duplicate Removal|✅|❌|
|Best for Character Counting|❌|✅|
|Best for First Non-Repeated Character|❌|✅|

---

# Interview Questions

### Q1. Why use a Dictionary?

Because it stores the frequency of each character.

---

### Q2. Why are two traversals required?

- First traversal → Count frequencies.
- Second traversal → Find the first character with frequency `1`.

---

### Q3. Can HashSet solve this problem?

A single `HashSet` cannot because it does not store frequencies.

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

> To find the first non-repeated character, I use a `Dictionary<char, int>` to count the frequency of each character in the string. After counting, I traverse the original string again and return the first character whose frequency is `1`. This preserves the original order and runs in **O(n)** time with **O(n)** extra space.

---

# Key Points to Remember

- Use `Dictionary<char, int>` for frequency counting.
- A `HashSet` alone cannot solve this problem because it doesn't store counts.
- Traverse the string **twice**:
  - First pass → Count frequencies.
  - Second pass → Find the first character with frequency `1`.
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(n)`
- **Best Interview Approach:** Dictionary + Frequency Count.