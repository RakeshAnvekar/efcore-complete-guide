# Missing Number in an Array (Sorting & HashSet Approaches)

> **Course:** Data Structures & Algorithms using C#

**Topic:** Missing Number in an Array

**Approaches Covered**

- Sorting
- HashSet

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐☆

---

# Table of Contents

1. Problem Statement
2. Approach 3 – Sorting
3. Dry Run
4. Time Complexity
5. Space Complexity
6. Approach 4 – HashSet
7. Dry Run
8. Time Complexity
9. Space Complexity
10. Comparison
11. Interview Questions
12. Summary
13. Key Takeaways

---

# Problem Statement

Given an array containing numbers from **1 to N**, where one number is missing, find the missing number.

Example

```
Input

4 2 1 5

Output

3
```

---

# Approach 3 – Sorting

## Idea

Sort the array first.

After sorting, every element should be present in its expected position.

Expected

```
Index     Expected Value

0              1

1              2

2              3

3              4

4              5
```

After sorting

```
1 2 4 5
```

Compare each element with its expected value.

As soon as a mismatch occurs,

that expected value is the missing number.

---

# Algorithm

Step 1

Sort the array.

```
4 2 1 5

↓

1 2 4 5
```

Step 2

Traverse the sorted array.

Step 3

Compare

```
numbers[i]

with

i + 1
```

If they are different,

```
i + 1
```

is the missing number.

---

# Code Using `for`

```csharp
using System;

public class Program
{
    public static void Main()
    {
        int[] numbers = {4,2,1,5};

        Array.Sort(numbers);

        for(int i = 0; i < numbers.Length; i++)
        {
            if(numbers[i] != i + 1)
            {
                Console.WriteLine("Missing Number : " + (i + 1));
                break;
            }
        }
    }
}
```

---

# Dry Run

Input

```
4 2 1 5
```

After Sorting

```
1 2 4 5
```

Iteration 1

```
i = 0

Expected = 1

Actual = 1

Match
```

---

Iteration 2

```
i = 1

Expected = 2

Actual = 2

Match
```

---

Iteration 3

```
i = 2

Expected = 3

Actual = 4

Mismatch
```

Therefore

```
Missing Number = 3
```

---

# Visual Representation

```
Sorted Array

Index

0   1   2   3

Value

1   2   4   5

Expected

1   2   3   4

            ↑

Mismatch

↓

Missing Number = 3
```

---

# Time Complexity

Sorting

```
O(n log n)
```

Traversal

```
O(n)
```

Overall

```
O(n log n)
```

---

# Space Complexity

```
O(1)
```

(If sorting is performed in-place.)

---

# Approach 4 – Using HashSet

## Idea

Store every element in a HashSet.

Then check numbers from **1 to N**.

The first number that is not found in the HashSet is the missing number.

---

# Algorithm

Input

```
1 2 4 5
```

Create HashSet

```
{1,2,4,5}
```

Now check

```
1

Exists ✔

2

Exists ✔

3

Not Found ❌
```

Therefore

```
Missing Number = 3
```

---

# Code Using `foreach`

```csharp
using System;
using System.Collections.Generic;

public class Program
{
    public static void Main()
    {
        int[] numbers = {1,2,4,5};

        int n = 5;

        HashSet<int> set = new HashSet<int>();

        foreach(int num in numbers)
        {
            set.Add(num);
        }

        for(int i = 1; i <= n; i++)
        {
            if(!set.Contains(i))
            {
                Console.WriteLine("Missing Number : " + i);
                break;
            }
        }
    }
}
```

---

# Dry Run

Input

```
1 2 4 5
```

Initially

```
HashSet

{}
```

Add

```
1

↓

{1}
```

Add

```
2

↓

{1,2}
```

Add

```
4

↓

{1,2,4}
```

Add

```
5

↓

{1,2,4,5}
```

Now search

```
1

Found ✔
```

```
2

Found ✔
```

```
3

Not Found ❌
```

Therefore

```
Missing Number = 3
```

---

# Visual Representation

```
Array

1 2 4 5

↓

HashSet

{1,2,4,5}

↓

Check

1 ✔

2 ✔

3 ❌

↓

Missing Number = 3
```

---

# Time Complexity

Building HashSet

```
O(n)
```

Searching

```
O(n)
```

Overall

```
O(n)
```

---

# Space Complexity

```
O(n)
```

Extra memory is used for the HashSet.

---

# Comparison

| Approach | Time | Space | Best For |
|-----------|------|--------|----------|
| Sorting | O(n log n) | O(1) | When sorting is acceptable |
| HashSet | O(n) | O(n) | Fast lookup without sorting |
| Sum Formula | O(n) | O(1) | Best interview solution |
| XOR | O(n) | O(1) | Advanced interview solution |

---

# Interview Questions

## Why is the Sorting approach slower?

Because sorting itself takes

```
O(n log n)
```

which is slower than a single traversal.

---

## Why is HashSet faster?

HashSet provides average

```
O(1)
```

lookup time using hashing.

---

## Which approach is best in interviews?

If the array contains numbers from **1 to N** and only one number is missing,

the **Sum Formula** or **XOR** approach is preferred because both have

- Time Complexity: **O(n)**
- Space Complexity: **O(1)**

---

## When should we use HashSet?

Use HashSet when

- The array is unsorted.
- Fast lookup is required.
- Extra memory is allowed.

---

# Summary

There are multiple ways to find the missing number.

- **Sorting** is simple to understand but slower because of the sorting step.
- **HashSet** provides fast lookups at the cost of additional memory.
- In coding interviews, **Sum Formula** and **XOR** are generally considered the optimal solutions because they achieve **O(n)** time with **O(1)** extra space.

---

# Key Takeaways

✔ Sorting first makes comparison easy.

✔ HashSet provides constant-time lookups.

✔ Sorting takes **O(n log n)** time.

✔ HashSet takes **O(n)** time and **O(n)** space.

✔ Sum Formula and XOR remain the optimal interview solutions.

---

# Next Problem

## Find Duplicate Element in an Array

Topics Covered

- Nested Loop Approach
- HashSet Approach
- Dictionary Approach
- Floyd's Cycle Detection
- Time & Space Complexity
- Interview Questions