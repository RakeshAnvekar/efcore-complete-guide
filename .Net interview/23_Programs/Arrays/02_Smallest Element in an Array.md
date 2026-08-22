# Smallest Element in an Array

> **Course:** Data Structures & Algorithms using C#

**Topic:** Smallest Element in an Array

**Difficulty:** ⭐⭐☆☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Problem Statement
2. Algorithm
3. Approach 1 – Using `for` Loop
4. Dry Run (`for` Loop)
5. Approach 2 – Using `foreach`
6. Dry Run (`foreach`)
7. Using LINQ
8. Edge Cases
9. Time Complexity
10. Space Complexity
11. Interview Questions
12. Summary
13. Key Takeaways

---

# Problem Statement

Given an array of integers, find the smallest element.

Example

```
Input

[10, 5, 25, 8, 15]

Output

5
```

---

# Algorithm

1. Assume the first element is the smallest.
2. Traverse the remaining elements.
3. Compare each element with the current smallest.
4. If the current element is smaller, update the smallest.
5. Return the smallest element.

---

# Approach 1 – Using `for` Loop

## Code

```csharp
using System;

public class Program
{
    public static void Main(string[] args)
    {
        int[] numbers = {10, 5, 25, 8, 15};

        int smallest = numbers[0];

        for (int i = 1; i < numbers.Length; i++)
        {
            if (numbers[i] < smallest)
            {
                smallest = numbers[i];
            }
        }

        Console.WriteLine($"Smallest Element : {smallest}");
    }
}
```

---

# Dry Run (`for` Loop)

Array

```
[10, 5, 25, 8, 15]
```

Initially

```
smallest = 10
```

### Iteration 1

```
i = 1

numbers[i] = 5

5 < 10 ?

True

smallest = 5
```

---

### Iteration 2

```
i = 2

numbers[i] = 25

25 < 5 ?

False

smallest = 5
```

---

### Iteration 3

```
i = 3

numbers[i] = 8

8 < 5 ?

False

smallest = 5
```

---

### Iteration 4

```
i = 4

numbers[i] = 15

15 < 5 ?

False

smallest = 5
```

---

Final Answer

```
5
```

---

# Visual Representation

```
Index

0   1   2   3   4

10  5  25  8  15

smallest = 10

↓

Compare 5

↓

smallest = 5

↓

Compare 25

↓

smallest = 5

↓

Compare 8

↓

smallest = 5

↓

Compare 15

↓

smallest = 5
```

---

# Approach 2 – Using `foreach`

`foreach` iterates directly over each element.

## Code

```csharp
using System;

public class Program
{
    public static void Main(string[] args)
    {
        int[] numbers = {10, 5, 25, 8, 15};

        int smallest = numbers[0];

        foreach (int number in numbers)
        {
            if (number < smallest)
            {
                smallest = number;
            }
        }

        Console.WriteLine($"Smallest Element : {smallest}");
    }
}
```

---

# Dry Run (`foreach`)

Array

```
[10, 5, 25, 8, 15]
```

Initially

```
smallest = 10
```

### First Iteration

```
number = 10

10 < 10 ?

False

smallest = 10
```

---

### Second Iteration

```
number = 5

5 < 10 ?

True

smallest = 5
```

---

### Third Iteration

```
number = 25

25 < 5 ?

False

smallest = 5
```

---

### Fourth Iteration

```
number = 8

8 < 5 ?

False

smallest = 5
```

---

### Fifth Iteration

```
number = 15

15 < 5 ?

False

smallest = 5
```

---

Final Answer

```
5
```

---

# Approach 3 – Using LINQ

```csharp
using System;
using System.Linq;

public class Program
{
    public static void Main(string[] args)
    {
        int[] numbers = {10, 5, 25, 8, 15};

        int smallest = numbers.Min();

        Console.WriteLine($"Smallest Element : {smallest}");
    }
}
```

> **Interview Tip:** Although `Min()` is convenient, interviewers generally expect the manual solution using a loop.

---

# Edge Cases

## Case 1 – Single Element

```
Input

[5]

Output

5
```

---

## Case 2 – All Negative Numbers

```
Input

[-10, -20, -5]

Output

-20
```

---

## Case 3 – Duplicate Smallest Elements

```
Input

[5, 10, 5, 20]

Output

5
```

---

## Case 4 – Already Sorted

```
Input

[5, 10, 15, 20]

Output

5
```

---

# Time Complexity

Both `for` and `foreach` traverse the array only once.

```
O(n)
```

---

# Space Complexity

Only one extra variable (`smallest`) is used.

```
O(1)
```

---

# `for` vs `foreach`

| Feature | `for` | `foreach` |
|----------|--------|-----------|
| Uses Index | ✅ Yes | ❌ No |
| Can Modify Elements | ✅ Yes | ❌ No |
| Simpler Syntax | ❌ | ✅ |
| Performance | Almost Same | Almost Same |
| Best For | When index is required | Read-only traversal |

---

# Why Initialize with the First Element?

Correct

```csharp
int smallest = numbers[0];
```

Wrong

```csharp
int smallest = 0;
```

Example

```
[5, 10, 20]
```

If

```csharp
smallest = 0;
```

The answer would incorrectly remain `0`, even though `0` is not present in the array.

Initializing with the first element works for both positive and negative numbers.

---

# Interview Questions

## Why do we initialize `smallest` with `numbers[0]`?

Because the first element is guaranteed to exist in the array (assuming it's not empty). This ensures the algorithm works correctly for both positive and negative values.

---

## What is the Time Complexity?

```
O(n)
```

The array is traversed only once.

---

## What is the Space Complexity?

```
O(1)
```

Only one additional variable is used.

---

## Which is better: `for` or `foreach`?

- Use **`for`** when you need the index or need to modify array elements.
- Use **`foreach`** when you only need to read elements because it is simpler and more readable.

---

# Summary

To find the smallest element in an array:

1. Initialize the smallest element with the first array element.
2. Traverse the remaining elements.
3. Compare each element with the current smallest.
4. Update the smallest whenever a smaller element is found.
5. Return the final smallest value.

Both `for` and `foreach` solve the problem efficiently with **O(n)** time complexity and **O(1)** space complexity.

---

# Key Takeaways

✔ Initialize `smallest` with the first element.

✔ Traverse the array only once.

✔ Update `smallest` whenever a smaller value is found.

✔ `for` is useful when the index is needed.

✔ `foreach` is ideal for read-only traversal.

✔ Time Complexity: **O(n)**

✔ Space Complexity: **O(1)**

---

# Next Problem

## Second Largest Element in an Array

Topics covered:

- Single Traversal Approach
- Handling Duplicate Values
- `for` Loop Solution
- `foreach` Loop Solution
- Dry Run
- Interview Questions
- Time & Space Complexity