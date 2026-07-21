# Longest Word in a Sentence (C# Interview Notes)

## Problem Statement

Given a sentence, find the **longest word**.

### Example

```text
Input  : I love programming in CSharp

Output : programming
```

```text
Input  : ChatGPT is amazing

Output : ChatGPT
```

---

# Approach

1. Split the sentence into words.
2. Assume the first word is the longest.
3. Traverse all the words.
4. Compare the length of each word with the current longest word.
5. Update the longest word if a longer word is found.
6. Print the longest word.

---

# Algorithm

1. Read the sentence.
2. Split the sentence using `Split()`.
3. Store the first word as the longest.
4. Traverse the remaining words.
5. Compare the length of each word.
6. If a word is longer, update the longest word.
7. Print the longest word.

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
Longest = I

love > I

↓

Longest = love

programming > love

↓

Longest = programming

in < programming

↓

No Change

CSharp < programming

↓

No Change
```

Output

```text
programming
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

        string[] words = sentence.Split(' ');

        string longestWord = words[0];

        foreach (string word in words)
        {
            if (word.Length > longestWord.Length)
            {
                longestWord = word;
            }
        }

        Console.WriteLine("Longest Word: " + longestWord);
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
string[] words = sentence.Split(' ');
```

Split the sentence into individual words.

```text
I
love
programming
in
CSharp
```

---

## Step 3

```csharp
string longestWord = words[0];
```

Assume the first word is the longest.

Initially

```text
Longest = I
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
if (word.Length > longestWord.Length)
{
    longestWord = word;
}
```

Compare the length of the current word with the longest word.

If the current word is longer, update `longestWord`.

Example

```text
love > I

↓

Longest = love
```

```text
programming > love

↓

Longest = programming
```

---

## Step 6

```csharp
Console.WriteLine("Longest Word: " + longestWord);
```

Print the longest word.

Output

```text
Longest Word: programming
```

---

# Output

```text
Longest Word: programming
```

---

# Time Complexity

The sentence is traversed only once.

**Time Complexity: O(n)**

where **n** is the number of words (or linear in the input size).

---

# Space Complexity

An array of words is created using `Split()`.

**Space Complexity: O(n)**

---

# Better Approach

Use

```csharp
sentence.Split(' ', StringSplitOptions.RemoveEmptyEntries);
```

This ignores extra spaces in the sentence.

Example

```text
I   love   programming
```

becomes

```text
I
love
programming
```

instead of creating empty strings.

---

# Interview Questions

### Q1. How do you find the longest word in a sentence?

Split the sentence into words and compare the length of each word.

---

### Q2. Why do we initialize with the first word?

So that we always have a valid word to compare with.

---

### Q3. What is the time complexity?

**O(n)** because each word is checked only once.

---

### Q4. Why use `RemoveEmptyEntries`?

It removes empty strings created by multiple consecutive spaces.

---

# Interview Answer

> Split the sentence into words using `Split()`. Assume the first word is the longest, then traverse the remaining words and compare their lengths. Whenever a longer word is found, update the longest word. After the loop, print the longest word.

---

# Key Points

- Split the sentence into words.
- Assume the first word is the longest.
- Compare the length of each word.
- Update the longest word whenever a longer word is found.
- Use `RemoveEmptyEntries` to handle extra spaces.
- **Time Complexity:** **O(n)**
- **Space Complexity:** **O(n)**