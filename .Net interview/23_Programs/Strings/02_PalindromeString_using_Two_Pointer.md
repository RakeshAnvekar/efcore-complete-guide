# Palindrome String using Two Pointer (C# Interview Notes)

## Problem Statement

Given a string, determine whether it is a **Palindrome**.

A palindrome is a string that reads the same from **left to right** and **right to left**.

### Example

```text
Input        Output
------------ ----------------
madam        Palindrome
racecar      Palindrome
level        Palindrome
hello        Not Palindrome
abc          Not Palindrome
```

---

# What is Two Pointer Technique?

The **Two Pointer Technique** uses two indexes.

- One starts from the **beginning** of the string.
- One starts from the **end** of the string.

Both pointers move toward the center.

If all characters match, the string is a palindrome.

---

# Visualization

Example

```text
String

m a d a m

0 1 2 3 4
```

Initially

```text
m a d a m
↑       ↑
L       R
```

Compare

```text
m == m ✔
```

Move both pointers

```text
m a d a m
  ↑   ↑
  L   R
```

Compare

```text
a == a ✔
```

Move again

```text
m a d a m
    ↑
 L==R
```

Loop Ends

Output

```text
Palindrome
```

---

# Algorithm

1. Initialize

```text
left = 0

right = text.Length - 1
```

2. Compare

```text
text[left]

text[right]
```

3. If they are different

```text
Not Palindrome
```

4. Otherwise

```text
left++

right--
```

5. Repeat until

```text
left >= right
```

---

# Dry Run

Input

```text
madam
```

Initially

```text
left = 0

right = 4
```

---

### Iteration 1

```text
text[0] = m

text[4] = m

m == m ✔
```

Move

```text
left = 1

right = 3
```

---

### Iteration 2

```text
text[1] = a

text[3] = a

a == a ✔
```

Move

```text
left = 2

right = 2
```

Loop Ends

Output

```text
Palindrome
```

---

# Dry Run Table

| Left | Right | Characters | Match |
|-----:|------:|-----------|-------|
|0|4|m == m|✔|
|1|3|a == a|✔|
|2|2|Stop|Palindrome|

---

# Dry Run (Not Palindrome)

Input

```text
hello
```

Initially

```text
h e l l o
↑       ↑
```

Compare

```text
h != o
```

Immediately

```text
Not Palindrome
```

No further comparisons are needed.

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string text = "madam";

        int left = 0;
        int right = text.Length - 1;

        bool isPalindrome = true;

        while (left < right)
        {
            if (text[left] != text[right])
            {
                isPalindrome = false;
                break;
            }

            left++;
            right--;
        }

        if (isPalindrome)
            Console.WriteLine("Palindrome");
        else
            Console.WriteLine("Not Palindrome");
    }
}
```

---

# Why Do We Use `left < right`?

Suppose

```text
madam

m a d a m
    ↑
```

When both pointers meet at the middle character

```text
d
```

there is no need to compare it with itself.

Therefore

```csharp
while(left < right)
```

is sufficient.

---

# Why Do We Use `break`?

Suppose

```text
hello
```

First comparison

```text
h != o
```

The answer is already known.

There is no need to compare

```text
e

l

l
```

So

```csharp
break;
```

improves efficiency.

---

# Time Complexity

Suppose

```text
madam
```

Comparisons

```text
m == m

a == a
```

Only half of the string is checked.

Therefore

```text
Time Complexity = O(n)
```

---

# Space Complexity

Only three variables are used.

- left
- right
- isPalindrome

No extra string or array is created.

```text
Space Complexity = O(1)
```

---

# Edge Cases

## Case 1

```text
Input

a

Output

Palindrome
```

---

## Case 2

```text
Input

""

Output

Palindrome
```

---

## Case 3

Case Sensitive

```text
Input

Level
```

By default

```text
L != l
```

Use

```csharp
text = text.ToLower();
```

for case-insensitive comparison.

---

## Case 4

Ignore Spaces

```text
A man a plan a canal Panama
```

Use

```csharp
text = text.Replace(" ", "").ToLower();
```

---

# Common Mistakes

## Mistake 1

Using

```csharp
while(left <= right)
```

Correct

```csharp
while(left < right)
```

The middle character does not need comparison.

---

## Mistake 2

Forgetting

```csharp
break;
```

The program continues checking even after finding a mismatch.

---

## Mistake 3

Using

```csharp
right = text.Length;
```

Wrong.

Correct

```csharp
right = text.Length - 1;
```

because indexing starts at **0**.

---

# Interview Questions

### Q1. Why is Two Pointer better than reversing?

Because it does not create another string.

Memory usage is

```text
O(1)
```

instead of

```text
O(n)
```

---

### Q2. Why do we stop when `left >= right`?

Because every character pair has already been compared.

---

### Q3. What is the time complexity?

```text
O(n)
```

---

### Q4. What is the space complexity?

```text
O(1)
```

---

# Interview Answer

> The Two Pointer approach initializes one pointer at the beginning of the string and another at the end. During each iteration, the characters at both pointers are compared. If they differ, the string is not a palindrome, and the loop stops immediately. If they match, both pointers move toward the center. Since each character is compared at most once and no extra data structure is created, the algorithm has **O(n)** time complexity and **O(1)** space complexity.

---

# Key Points to Remember

- Two pointers:
  - `left = 0`
  - `right = text.Length - 1`
- Compare both characters.
- If mismatch → **Not Palindrome**.
- If match → Move pointers inward.
- Stop when `left >= right`.
- Time Complexity → **O(n)**
- Space Complexity → **O(1)**
- Preferred interview solution → **Two Pointer Technique**