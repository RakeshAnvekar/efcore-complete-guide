# Longest Common Prefix (C# Interview Notes)

> **Interview Importance:** ⭐⭐⭐⭐⭐

Longest Common Prefix (LCP) is one of the most common string interview questions asked in Microsoft, Amazon, Google, Adobe, Infosys, and TCS. It tests your understanding of arrays, strings, loops, and comparison logic.

---

# Problem Statement

Given an array of strings, find the **longest common prefix** shared among all the strings.

If there is no common prefix, return an empty string.

---

# Example 1

```text
Input

["flower","flow","flight"]

Output

"fl"
```

---

# Example 2

```text
Input

["dog","racecar","car"]

Output

""
```

There is no common prefix.

---

# Example 3

```text
Input

["apple","app","application"]

Output

"app"
```

---

# Interview Approach (Horizontal Scanning) ⭐⭐⭐⭐⭐

## Idea

Take the first word as the initial prefix.

Compare it with every other word.

If the word doesn't start with the prefix, keep removing the last character from the prefix until it matches.

---

# Visualization

Input

```text
["flower","flow","flight"]
```

Initial Prefix

```text
flower
```

Compare with

```text
flow
```

```
flower

flow

↓

flow
```

Current Prefix

```text
flow
```

Compare with

```text
flight
```

```
flow

flight

↓

flo

↓

fl
```

Answer

```text
fl
```

---

# Algorithm

1. If array is empty return "".
2. Take first word as prefix.
3. Traverse remaining strings.
4. While current string doesn't start with prefix
   - Remove last character from prefix.
5. Continue until all strings are checked.
6. Return prefix.

---

# Dry Run

Input

```text
["flower","flow","flight"]
```

Initially

```
prefix = flower
```

---

## Compare with "flow"

```
flower

flow
```

Not equal.

Remove last character.

```
flowe
```

Still doesn't match.

Remove again.

```
flow
```

Now

```
flow starts with flow

✔
```

Current prefix

```
flow
```

---

## Compare with "flight"

```
flow

flight
```

Not matching.

Remove

```
flo
```

Still doesn't match.

Remove

```
fl
```

Now

```
flight starts with fl

✔
```

Answer

```
fl
```

---

# Dry Run Table

| Prefix | Current Word | Action |
|---------|--------------|--------|
| flower | flow | Remove "er" |
| flow | flight | Remove "ow" |
| fl | flight | Match |
| Final | - | fl |

---

# C# Code

```csharp
using System;

class Program
{
    static string LongestCommonPrefix(string[] words)
    {
        if (words.Length == 0)
            return "";

        string prefix = words[0];

        for (int i = 1; i < words.Length; i++)
        {
            while (!words[i].StartsWith(prefix))
            {
                prefix = prefix.Substring(0, prefix.Length - 1);

                if (prefix == "")
                    return "";
            }
        }

        return prefix;
    }

    static void Main()
    {
        string[] words = { "flower", "flow", "flight" };

        Console.WriteLine(LongestCommonPrefix(words));
    }
}
```

---

# Code Explanation

---

## Step 1

```csharp
if(words.Length==0)
    return "";
```

If array is empty,

No common prefix exists.

---

## Step 2

```csharp
string prefix = words[0];
```

Initially

```
prefix = flower
```

---

## Step 3

```csharp
for(int i=1;i<words.Length;i++)
```

Compare prefix with every remaining word.

Iteration

```
flow

↓

flight
```

---

## Step 4

```csharp
while(!words[i].StartsWith(prefix))
```

Means

```
Does current word begin with prefix?
```

Example

```
flow

StartsWith("flower") ?

False
```

---

## Step 5

```csharp
prefix = prefix.Substring(0,prefix.Length-1);
```

Remove the last character.

Example

```
flower

↓

flowe

↓

flow
```

Keep shrinking until it matches.

---

## Step 6

```csharp
if(prefix=="")
    return "";
```

Suppose

```
dog

racecar
```

Eventually

```
prefix=""

Return ""
```

---

## Step 7

```csharp
return prefix;
```

Return the longest common prefix.

---

# Complete Execution

Input

```
flower
flow
flight
```

```
prefix = flower

↓

flow doesn't start with flower

↓

flowe

↓

flow

↓

flight doesn't start with flow

↓

flo

↓

fl

↓

Return fl
```

---

# Time Complexity

Worst Case

```
O(n × m)
```

where

- n = Number of strings
- m = Length of shortest string

---

# Space Complexity

```
O(1)
```

No extra data structures are used.

---

# Edge Cases

### Empty Array

```
Input

[]

Output

""
```

---

### One String

```
Input

["apple"]

Output

apple
```

---

### No Common Prefix

```
Input

["dog","cat"]

Output

""
```

---

### Entire Word Common

```
Input

["app","app","app"]

Output

app
```

---

# Common Mistakes

## Mistake 1

Not checking for empty array.

---

## Mistake 2

Using `Contains()` instead of `StartsWith()`.

Wrong

```csharp
word.Contains(prefix)
```

Correct

```csharp
word.StartsWith(prefix)
```

---

## Mistake 3

Removing the first character instead of the last.

Wrong

```
flower

↓

lower
```

Correct

```
flower

↓

flowe

↓

flow
```

---

# Interview Questions

### Q1. Which approach is used?

Horizontal Scanning.

---

### Q2. Why use `StartsWith()`?

Because the prefix must appear at the **beginning** of every string.

---

### Q3. What is the time complexity?

```
O(n × m)
```

---

### Q4. What is the space complexity?

```
O(1)
```

---

### Q5. Can this be solved another way?

Yes.

- Horizontal Scanning ✅
- Vertical Scanning
- Divide and Conquer
- Trie (Advanced)

---

# Interview Answer

> The solution uses the Horizontal Scanning approach. The first string is considered the initial prefix. Each remaining string is checked using `StartsWith()`. If the string does not start with the current prefix, the last character of the prefix is removed repeatedly until a match is found. After all strings are processed, the remaining prefix is the longest common prefix. The algorithm runs in **O(n × m)** time and **O(1)** extra space.

---

# Key Points to Remember

- Start with the first string as the prefix.
- Compare it with every other string.
- Use `StartsWith()` to check the prefix.
- Remove one character from the end until it matches.
- **Time Complexity:** O(n × m)
- **Space Complexity:** O(1)
- **Interview Preferred:** Horizontal Scanning.