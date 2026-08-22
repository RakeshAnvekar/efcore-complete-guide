# Shortest Word in a Sentence (C# Interview Notes)

## Problem Statement

Given a sentence, find the **shortest word**.

### Example

```text
Input  : I love programming in CSharp

Output : I
```

```text
Input  : ChatGPT is amazing

Output : is
```

---

# Approach

1. Split the sentence into words.
2. Assume the first word is the shortest.
3. Traverse all the words.
4. Compare the length of each word with the current shortest word.
5. Update the shortest word if a smaller word is found.
6. Print the shortest word.

---

# Algorithm

1. Read the sentence.
2. Split the sentence into words.
3. Store the first word as the shortest.
4. Traverse the remaining words.
5. Compare the length of each word.
6. If a word is shorter, update the shortest word.
7. Print the shortest word.

---

# Dry Run

```text
Sentence

I love programming in CSharp
```

Split

```text
I
love
programming
in
CSharp
```

Comparison

```text
Shortest = I

love > I

↓

No Change

programming > I

↓

No Change

in > I

↓

No Change

CSharp > I

↓

No Change
```

Output

```text
I
```

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string sentence = "I love programming in CSharp";

        string[] words = sentence.Split(' ', StringSplitOptions.RemoveEmptyEntries);

        string shortestWord = words[0];

        foreach (string word in words)
        {
            if (word.Length < shortestWord.Length)
            {
                shortestWord = word;
            }
        }

        Console.WriteLine("Shortest Word: " + shortestWord);
    }
}
```

---

# Program Explanation

## Step 1

```csharp
string sentence = "I love programming in CSharp";
```

Input sentence.

---

## Step 2

```csharp
string[] words = sentence.Split(' ', StringSplitOptions.RemoveEmptyEntries);
```

Split the sentence into individual words.

```text
I
love
programming
in
CSharp
```

`RemoveEmptyEntries` ignores extra spaces.

---

## Step 3

```csharp
string shortestWord = words[0];
```

Assume the first word is the shortest.

Initially

```text
Shortest = I
```

---

## Step 4

```csharp
foreach (string word in words)
```

Traverse each word.

---

## Step 5

```csharp
if (word.Length < shortestWord.Length)
{
    shortestWord = word;
}
```

Compare the length of the current word with the shortest word.

If the current word is shorter, update `shortestWord`.

Example

```text
Sentence: Hello C# Programming

Initial

Shortest = Hello

Compare

C#

Length = 2

↓

2 < 5

↓

Shortest = C#
```

---

## Step 6

```csharp
Console.WriteLine("Shortest Word: " + shortestWord);
```

Print the shortest word.

Output

```text
Shortest Word: I
```

---

# Output

```text
Shortest Word: I
```

---

# Time Complexity

The sentence is traversed only once.

**Time Complexity: O(n)**

where **n** is the number of words.

---

# Space Complexity

An array of words is created using `Split()`.

**Space Complexity: O(n)**

---

# Better Approach

Always use

```csharp
sentence.Split(' ', StringSplitOptions.RemoveEmptyEntries);
```

This prevents empty strings when the sentence contains multiple spaces.

---

# Interview Questions

### Q1. How do you find the shortest word in a sentence?

Split the sentence into words and compare the length of each word with the current shortest word.

---

### Q2. Why do we initialize with the first word?

It provides a valid starting point for comparison.

---

### Q3. What is the time complexity?

**O(n)** because each word is checked only once.

---

### Q4. Why use `RemoveEmptyEntries`?

It removes empty strings created by multiple consecutive spaces.

---

# Interview Answer

> Split the sentence into words using `Split()`. Assume the first word is the shortest, then traverse the remaining words and compare their lengths. Whenever a shorter word is found, update the shortest word. After the loop, print the shortest word.

---

# Key Points

- Split the sentence into words.
- Assume the first word is the shortest.
- Compare the length of each word.
- Update the shortest word whenever a shorter word is found.
- Use `RemoveEmptyEntries` to handle extra spaces.
- **Time Complexity:** **O(n)**
- **Space Complexity:** **O(n)**