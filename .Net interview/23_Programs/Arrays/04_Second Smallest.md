# Second Smallest Element in an Array

> **Course:** Data Structures & Algorithms using C#

**Topic:** Second Smallest Element in an Array

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Problem Statement
2. Algorithm
3. Approach 1 – Using `for` Loop
4. Dry Run (`for` Loop)
5. Approach 2 – Using `foreach`
6. Dry Run (`foreach`)
7. Handling Duplicate Values
8. Edge Cases
9. Time Complexity
10. Space Complexity
11. Interview Questions
12. Summary
13. Key Takeaways

---

# Problem Statement

Given an array of integers, find the **second smallest** element.

Example

```
Input

[10, 5, 25, 8, 15]

Output

8
```

---

# Algorithm

Maintain two variables.

```
smallest

secondSmallest
```

Initially

```
smallest = int.MaxValue

secondSmallest = int.MaxValue
```

Traverse the array.

If current element is smaller than smallest

```
secondSmallest = smallest

smallest = current element
```

Otherwise

If current element is

- Greater than smallest
- Smaller than secondSmallest

Update secondSmallest.

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

        int smallest = int.MaxValue;
        int secondSmallest = int.MaxValue;

        for (int i = 0; i < numbers.Length; i++)
        {
            if (numbers[i] < smallest)
            {
                secondSmallest = smallest;
                smallest = numbers[i];
            }
            else if (numbers[i] < secondSmallest && numbers[i] != smallest)
            {
                secondSmallest = numbers[i];
            }
        }

        Console.WriteLine($"Smallest : {smallest}");
        Console.WriteLine($"Second Smallest : {secondSmallest}");
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
smallest = 2147483647

secondSmallest = 2147483647
```

---

## Iteration 1

Current

```
10
```

```
10 < smallest ?

Yes
```

Update

```
secondSmallest = smallest

smallest = 10
```

Result

```
smallest = 10

secondSmallest = MAX
```

---

## Iteration 2

Current

```
5
```

```
5 < 10 ?

Yes
```

Update

```
secondSmallest = 10

smallest = 5
```

Result

```
smallest = 5

secondSmallest = 10
```

---

## Iteration 3

Current

```
25
```

```
25 < 5 ?

No
```

```
25 < secondSmallest ?

No
```

Nothing changes.

---

## Iteration 4

Current

```
8
```

```
8 < 5 ?

No
```

```
8 < 10 ?

Yes
```

Update

```
secondSmallest = 8
```

Result

```
smallest = 5

secondSmallest = 8
```

---

## Iteration 5

Current

```
15
```

```
15 < 5 ?

No
```

```
15 < 8 ?

No
```

Nothing changes.

---

Final Answer

```
Smallest = 5

Second Smallest = 8
```

---

# Visual Representation

```
Array

10   5   25   8   15

↓

smallest = 10

secondSmallest = MAX

↓

smallest = 5

secondSmallest = 10

↓

smallest = 5

secondSmallest = 10

↓

smallest = 5

secondSmallest = 8

↓

smallest = 5

secondSmallest = 8
```

---

# Approach 2 – Using `foreach`

## Code

```csharp
using System;

public class Program
{
    public static void Main(string[] args)
    {
        int[] numbers = {10, 5, 25, 8, 15};

        int smallest = int.MaxValue;
        int secondSmallest = int.MaxValue;

        foreach (int number in numbers)
        {
            if (number < smallest)
            {
                secondSmallest = smallest;
                smallest = number;
            }
            else if (number < secondSmallest && number != smallest)
            {
                secondSmallest = number;
            }
        }

        Console.WriteLine($"Smallest : {smallest}");
        Console.WriteLine($"Second Smallest : {secondSmallest}");
    }
}
```

---

# Dry Run (`foreach`)

Array

```
[10, 5, 25, 8, 15]
```

### First Element

```
number = 10

smallest = 10

secondSmallest = MAX
```

---

### Second Element

```
number = 5

smallest = 5

secondSmallest = 10
```

---

### Third Element

```
number = 25

No Change
```

---

### Fourth Element

```
number = 8

secondSmallest = 8
```

---

### Fifth Element

```
number = 15

No Change
```

---

Final

```
Smallest = 5

Second Smallest = 8
```

---

# Handling Duplicate Values

Suppose

```
[5,5,8,10]
```

Correct Answer

```
Smallest = 5

Second Smallest = 8
```

Notice

We ignore duplicate smallest values.

This condition is important.

```csharp
number != smallest
```

Without it

```
5

5
```

would produce

```
Smallest = 5

Second Smallest = 5 ❌
```

---

# Edge Cases

## Case 1

```
Input

[5]

Output

No Second Smallest
```

---

## Case 2

```
Input

[5,5]

Output

No Second Smallest
```

---

## Case 3

```
Input

[-5,-10,-2]

Output

Smallest = -10

Second Smallest = -5
```

---

## Case 4

```
Input

[1,2]

Output

Second Smallest = 2
```

---

# Time Complexity

Only one traversal.

```
O(n)
```

---

# Space Complexity

Only two variables.

```
O(1)
```

---

# Why Use `int.MaxValue`?

Suppose

```
[10,20,30]
```

If

```csharp
smallest = 0;
```

Then

```
10 < 0 ?

False
```

The algorithm fails.

Using

```csharp
int.MaxValue
```

ensures every integer is smaller initially.

---

# Interview Questions

## Why use two variables?

One stores the smallest value.

The second stores the second smallest value.

This allows solving the problem in one traversal.

---

## Why check

```csharp
number != smallest
```

To ignore duplicate smallest values.

---

## Can this be solved in one traversal?

Yes.

Using

```
smallest

secondSmallest
```

Only one pass through the array is required.

---

## Time Complexity?

```
O(n)
```

---

## Space Complexity?

```
O(1)
```

---

## Can we sort the array?

Yes.

```csharp
Array.Sort(numbers);

Console.WriteLine(numbers[1]);
```

However,

Sorting takes

```
O(n log n)
```

The one-pass solution is more efficient.

---

# Summary

To find the second smallest element,

maintain two variables:

- `smallest`
- `secondSmallest`

Whenever a smaller value is found,

move the current smallest into `secondSmallest`.

Ignore duplicate smallest values.

This provides an efficient one-pass solution.

---

# Key Takeaways

✔ Use two variables.

✔ Initialize with `int.MaxValue`.

✔ Ignore duplicate smallest values.

✔ Solve in one traversal.

✔ Time Complexity

```
O(n)
```

✔ Space Complexity

```
O(1)
```

---

