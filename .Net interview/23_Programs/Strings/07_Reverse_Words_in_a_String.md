# Reverse Words in a String (Without Using Array.Reverse) - C# Interview Notes

> **Author:** ChatGPT  
> **Purpose:** .NET Interview Preparation

---

# Problem Statement

Given a sentence, reverse the **order of the words** without using the built-in `Array.Reverse()` method.

The characters inside each word should remain unchanged.

---

# Example

### Example 1

```text
Input

Hi i am Rakesh

Output

Rakesh am i Hi
```

---

### Example 2

```text
Input

Welcome To DotNet

Output

DotNet To Welcome
```

---

### Example 3

```text
Input

Hello World

Output

World Hello
```

---

# Algorithm

1. Read the input string.
2. Split the sentence into words using `Split(' ')`.
3. Start a loop from the last word to the first.
4. Append each word to the result string.
5. Add a space only if it is **not** the last word.
6. Print the final result.

---

# Dry Run

Input

```text
Hi i am Rakesh
```

---

### Step 1

Split the string

```text
["Hi", "i", "am", "Rakesh"]
```

---

### Step 2

Start from the last index.

```text
i = 3
```

Append

```text
Rakesh
```

Result

```text
Rakesh
```

---

### Step 3

```text
i = 2
```

Append

```text
am
```

Result

```text
Rakesh am
```

---

### Step 4

```text
i = 1
```

Append

```text
i
```

Result

```text
Rakesh am i
```

---

### Step 5

```text
i = 0
```

Append

```text
Hi
```

Result

```text
Rakesh am i Hi
```

---

# Dry Run Table

| Loop Index | Word | Result |
|------------|------|--------|
| 3 | Rakesh | Rakesh |
| 2 | am | Rakesh am |
| 1 | i | Rakesh am i |
| 0 | Hi | Rakesh am i Hi |

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string str = "Hi i am Rakesh";

        string[] words = str.Split(' ');

        string result = "";

        for (int i = words.Length - 1; i >= 0; i--)
        {
            result += words[i];

            if (i != 0)
            {
                result += " ";
            }
        }

        Console.WriteLine("Reversed Sentence = " + result);
    }
}
```

---

# Output

```text
Reversed Sentence = Rakesh am i Hi
```

---

# How It Works

```text
Hi i am Rakesh

↓

Split()

↓

[Hi] [i] [am] [Rakesh]

↓

Start from Last Index

↓

Rakesh

↓

am

↓

i

↓

Hi

↓

Rakesh am i Hi
```

---

# Why Use `if (i != 0)`?

If we always add a space:

```csharp
result += words[i];
result += " ";
```

The output becomes

```text
Rakesh am i Hi_
```

(`_` represents an extra space.)

To avoid the trailing space:

```csharp
if (i != 0)
{
    result += " ";
}
```

This adds spaces **only between words**, not after the last word.

---

# Time Complexity

- `Split()` → **O(n)**
- Loop through words → **O(n)**

Overall

```text
Time Complexity = O(n)
```

where

```text
n = Length of the input string
```

---

# Space Complexity

The array of words and the result string require extra memory.

```text
Space Complexity = O(n)
```

---

# Edge Cases

### Case 1

Input

```text
Hello
```

Output

```text
Hello
```

---

### Case 2

Input

```text
""
```

Output

```text
""
```

---

### Case 3

Input

```text
One Two Three Four
```

Output

```text
Four Three Two One
```

---

### Case 4

Input

```text
C# .NET Interview
```

Output

```text
Interview .NET C#
```

---

# Common Mistakes

### Mistake 1

Looping from the beginning.

Wrong

```csharp
for(int i = 0; i < words.Length; i++)
```

Correct

```csharp
for(int i = words.Length - 1; i >= 0; i--)
```

---

### Mistake 2

Adding a space after every word.

Wrong

```csharp
result += " ";
```

This creates a trailing space.

Correct

```csharp
if(i != 0)
{
    result += " ";
}
```

---

### Mistake 3

Trying to reverse the string instead of the words.

Wrong Output

```text
hsekaR ma i iH
```

Correct Output

```text
Rakesh am i Hi
```

---

# Interview Questions

### Q1. Why use `Split()`?

To separate the sentence into individual words.

---

### Q2. Why loop from the last index?

To print the words in reverse order.

---

### Q3. Why use `if (i != 0)`?

To avoid adding an extra space after the last word.

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

> To reverse the words without using `Array.Reverse()`, I first split the sentence into an array of words. Then I traverse the array from the last index to the first, appending each word to a result string. I add a space only between words to avoid a trailing space. This solution has a time complexity of **O(n)** and a space complexity of **O(n)**.

---

# Key Points to Remember

- Use `Split(' ')` to separate words.
- Traverse the array from the **last index to the first**.
- Add spaces only **between words**.
- Do **not** reverse the characters within each word.
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(n)`
- **Best Interview Approach (without `Array.Reverse()`):** Reverse traversal using a `for` loop.