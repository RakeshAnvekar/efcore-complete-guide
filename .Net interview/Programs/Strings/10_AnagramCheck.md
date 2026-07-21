# Anagram Check (C# Interview Notes)

## Problem Statement

Given two strings, check whether they are **Anagrams**.

Two strings are called **Anagrams** if they contain the same characters with the same frequency, but the order of the characters may be different.

### Example

```text
Input 1 : abcd
Input 2 : dcab

Output : Anagram
```

```text
Input 1 : hello
Input 2 : world

Output : Not Anagram
```

---

# Approach

1. Check whether both strings have the same length.
2. Convert both strings into character arrays.
3. Sort both character arrays.
4. Compare the characters one by one.
5. If every character matches, the strings are Anagrams.

---

# Algorithm

1. Read two strings.
2. Compare their lengths.
3. If lengths are different, print **Not Anagram**.
4. Convert both strings into character arrays.
5. Sort both arrays.
6. Compare each character.
7. If all characters match, print **Anagram**; otherwise, print **Not Anagram**.

---

# Dry Run

```text
String1 = abcd
String2 = dcab
```

Convert to arrays

```text
a b c d
d c a b
```

Sort both arrays

```text
a b c d
a b c d
```

Compare

```text
a == a ✔
b == b ✔
c == c ✔
d == d ✔
```

Output

```text
Anagram
```

---

# C# Program

```csharp
using System;

public class HelloWorld
{
    public static void Main(string[] args)
    {
        string str1 = "abcd";
        string str2 = "dcab";

        if (str1.Length != str2.Length)
        {
            Console.WriteLine("Not Anagram");
            return;
        }

        char[] str1CharArray = str1.ToCharArray();
        char[] str2CharArray = str2.ToCharArray();

        Array.Sort(str1CharArray);
        Array.Sort(str2CharArray);

        bool isAnagram = true;

        for (int i = 0; i < str1CharArray.Length; i++)
        {
            if (str1CharArray[i] != str2CharArray[i])
            {
                isAnagram = false;
                break;
            }
        }

        if (isAnagram)
            Console.WriteLine("Anagram");
        else
            Console.WriteLine("Not Anagram");
    }
}
```

---

# Program Explanation

## Step 1

```csharp
string str1 = "abcd";
string str2 = "dcab";
```

Two input strings.

```text
String1 = abcd
String2 = dcab
```

---

## Step 2

```csharp
if (str1.Length != str2.Length)
{
    Console.WriteLine("Not Anagram");
    return;
}
```

Compare the lengths of both strings.

If the lengths are different, they cannot be Anagrams.

Example

```text
cat

dogs

↓

Different lengths

↓

Not Anagram
```

---

## Step 3

```csharp
char[] str1CharArray = str1.ToCharArray();
char[] str2CharArray = str2.ToCharArray();
```

Convert both strings into character arrays.

```text
abcd

↓

a b c d
```

```text
dcab

↓

d c a b
```

---

## Step 4

```csharp
Array.Sort(str1CharArray);
Array.Sort(str2CharArray);
```

Sort both arrays alphabetically.

Before Sorting

```text
a b c d

d c a b
```

After Sorting

```text
a b c d

a b c d
```

---

## Step 5

```csharp
bool isAnagram = true;
```

Assume both strings are Anagrams.

If any character is different, change it to `false`.

---

## Step 6

```csharp
for (int i = 0; i < str1CharArray.Length; i++)
{
    if (str1CharArray[i] != str2CharArray[i])
    {
        isAnagram = false;
        break;
    }
}
```

Compare both sorted arrays character by character.

Comparison

```text
a == a ✔

b == b ✔

c == c ✔

d == d ✔
```

If any comparison fails,

```text
isAnagram = false
```

and the loop stops.

---

## Step 7

```csharp
if (isAnagram)
    Console.WriteLine("Anagram");
else
    Console.WriteLine("Not Anagram");
```

Print the final result.

Output

```text
Anagram
```

---

# Time Complexity

Sorting each string takes **O(n log n)**.

Comparing both arrays takes **O(n)**.

Overall

**Time Complexity : O(n log n)**

---

# Space Complexity

Character arrays are created.

**Space Complexity : O(n)**

---

# Better Approach

Instead of sorting, count the frequency of each character using an array or dictionary.

That approach has

**Time Complexity : O(n)**

---

# Approach Comparison

| Approach | Time | Space | Interview Preference |
|----------|------|-------|----------------------|
| Sorting | O(n log n) | O(n) | ⭐⭐⭐⭐ |
| Character Frequency | O(n) | O(n) | ⭐⭐⭐⭐⭐ |

---

# Interview Questions

### Q1. What is an Anagram?

Two strings are Anagrams if they contain the same characters with the same frequency, regardless of their order.

---

### Q2. Why do we compare string lengths first?

If the lengths are different, the strings cannot contain the same number of characters.

---

### Q3. Why do we sort both strings?

Sorting arranges the characters in the same order, making comparison easy.

---

### Q4. Why do we use a boolean variable?

It stores whether all characters matched during comparison.

---

### Q5. Can this problem be solved without sorting?

Yes.

Using character frequency counting, the time complexity becomes **O(n)**.

---

# Interview Answer

> To check whether two strings are Anagrams, first compare their lengths. If the lengths are different, they cannot be Anagrams. Otherwise, convert both strings into character arrays, sort them, and compare each character. If every character matches, the strings are Anagrams.

---

# Key Points

- Compare string lengths first.
- Convert strings to character arrays.
- Sort both arrays.
- Compare each character.
- Use a boolean flag to store the result.
- **Time Complexity:** **O(n log n)**
- **Space Complexity:** **O(n)**