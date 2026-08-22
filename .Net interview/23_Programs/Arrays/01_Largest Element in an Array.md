# Largest Element in an Array

> **Course:** Data Structures & Algorithms using C#

**Topic:** Largest Element in an Array

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

Given an array of integers, find the largest element.

Example

```
Input

[10, 5, 25, 8, 15]

Output

25
```

---

# Algorithm

1. Assume the first element is the largest.
2. Traverse the remaining elements.
3. Compare each element with the current largest.
4. If the current element is larger, update the largest.
5. Return the largest element.

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

        int largest = numbers[0];

        for (int i = 1; i < numbers.Length; i++)
        {
            if (numbers[i] > largest)
            {
                largest = numbers[i];
            }
        }

        Console.WriteLine($"Largest Element : {largest}");
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
largest = 10
```

### Iteration 1

```
i = 1

numbers[i] = 5

5 > 10 ?

False

largest = 10
```

---

### Iteration 2

```
i = 2

numbers[i] = 25

25 > 10 ?

True

largest = 25
```

---

### Iteration 3

```
i = 3

numbers[i] = 8

8 > 25 ?

False

largest = 25
```

---

### Iteration 4

```
i = 4

numbers[i] = 15

15 > 25 ?

False

largest = 25
```

---

Final Answer

```
25
```

---

# Visual Representation

```
Index

0   1   2   3   4

10  5  25  8  15

largest = 10

↓

Compare 5

↓

largest = 10

↓

Compare 25

↓

largest = 25

↓

Compare 8

↓

largest = 25

↓

Compare 15

↓

largest = 25
```

---

# Approach 2 – Using `foreach`

`foreach` iterates directly over each element, so you don't need an index.

## Code

```csharp
using System;

public class Program
{
    public static void Main(string[] args)
    {
        int[] numbers = {10, 5, 25, 8, 15};

        int largest = numbers[0];

        foreach (int number in numbers)
        {
            if (number > largest)
            {
                largest = number;
            }
        }

        Console.WriteLine($"Largest Element : {largest}");
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
largest = 10
```

### First Iteration

```
number = 10

10 > 10 ?

False

largest = 10
```

---

### Second Iteration

```
number = 5

5 > 10 ?

False

largest = 10
```

---

### Third Iteration

```
number = 25

25 > 10 ?

True

largest = 25
```

---

### Fourth Iteration

```
number = 8

8 > 25 ?

False

largest = 25
```

---

### Fifth Iteration

```
number = 15

15 > 25 ?

False

largest = 25
```

---

Final Answer

```
25
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

        int largest = numbers.Max();

        Console.WriteLine($"Largest Element : {largest}");
    }
}
```

> **Note:** LINQ is concise, but interviewers usually expect the manual implementation using a loop.

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

-5
```

---

## Case 3 – Duplicate Largest Elements

```
Input

[10, 20, 20, 15]

Output

20
```

---

## Case 4 – Already Sorted

```
Input

[5, 10, 15, 20]

Output

20
```

---

# Time Complexity

Both `for` and `foreach` traverse the array only once.

```
O(n)
```

---

# Space Complexity

Only one extra variable (`largest`) is used.

```
O(1)
```

---

# `for` vs `foreach`

| Feature | `for` | `foreach` |
|----------|--------|-----------|
| Uses Index | ✅ Yes | ❌ No |
| Can Modify Elements | ✅ Yes | ❌ No (read-only iteration) |
| Simpler Syntax | ❌ | ✅ |
| Performance | Almost Same | Almost Same |
| Best For | When index is required | When only reading values |

---

# Why Initialize with the First Element?

Correct

```csharp
int largest = numbers[0];
```

Wrong

```csharp
int largest = 0;
```

Example

```
[-5, -10, -2]
```

If

```csharp
largest = 0;
```

Result becomes

```
0
```

which is incorrect because `0` does not exist in the array.

Initializing with the first element works for both positive and negative numbers.

---

# Interview Questions

## Why do we initialize `largest` with `numbers[0]`?

Because the first element is guaranteed to be part of the array, ensuring the algorithm works correctly even when all elements are negative.

---

## What is the Time Complexity?

```
O(n)
```

One traversal of the array.

---

## What is the Space Complexity?

```
O(1)
```

Only one additional variable is used.

---

## Which is better: `for` or `foreach`?

- Use **`for`** when you need the index or want to modify elements.
- Use **`foreach`** when you only need to read elements. It is cleaner and easier to read.

---

# Summary

To find the largest element in an array:

1. Initialize the largest element with the first array element.
2. Traverse the array.
3. Compare each element with the current largest.
4. Update the largest whenever a bigger value is found.
5. Return the final largest value.

Both `for` and `foreach` provide an efficient solution with **O(n)** time complexity and **O(1)** space complexity.

---

# Key Takeaways

✔ Initialize `largest` with the first element.

✔ Traverse the array only once.

✔ Update `largest` whenever a bigger element is found.

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