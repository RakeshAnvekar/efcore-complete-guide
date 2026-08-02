# Second Largest Element in an Array

> **Course:** Data Structures & Algorithms using C#

**Topic:** Second Largest Element in an Array

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

Given an array of integers, find the **second largest** element.

Example

```
Input

[10, 5, 25, 8, 15]

Output

15
```

---

# Algorithm

Maintain two variables.

```
largest

secondLargest
```

Initially

```
largest = int.MinValue

secondLargest = int.MinValue
```

Traverse the array.

If current element is greater than largest

```
secondLargest = largest

largest = current element
```

Otherwise

If current element is

- Less than largest
- Greater than secondLargest

Update secondLargest.

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

        int largest = int.MinValue;
        int secondLargest = int.MinValue;

        for (int i = 0; i < numbers.Length; i++)
        {
            if (numbers[i] > largest)
            {
                secondLargest = largest;
                largest = numbers[i];
            }
            else if (numbers[i] > secondLargest && numbers[i] != largest)
            {
                secondLargest = numbers[i];
            }
        }

        Console.WriteLine($"Largest : {largest}");
        Console.WriteLine($"Second Largest : {secondLargest}");
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
largest = -2147483648

secondLargest = -2147483648
```

---

### Iteration 1

```
Current = 10

10 > largest

Yes

secondLargest = -2147483648

largest = 10
```

Result

```
largest = 10

secondLargest = -2147483648
```

---

### Iteration 2

```
Current = 5

5 > 10 ?

No

5 > secondLargest ?

Yes
```

Update

```
secondLargest = 5
```

Result

```
largest = 10

secondLargest = 5
```

---

### Iteration 3

```
Current = 25

25 > 10 ?

Yes
```

Update

```
secondLargest = 10

largest = 25
```

Result

```
largest = 25

secondLargest = 10
```

---

### Iteration 4

```
Current = 8

8 > 25 ?

No

8 > 10 ?

No
```

No changes.

---

### Iteration 5

```
Current = 15

15 > 25 ?

No

15 > 10 ?

Yes
```

Update

```
secondLargest = 15
```

Final Result

```
Largest = 25

Second Largest = 15
```

---

# Visual Representation

```
Array

10   5   25   8   15

↓

largest = 10

secondLargest = MIN

↓

largest = 10

secondLargest = 5

↓

largest = 25

secondLargest = 10

↓

largest = 25

secondLargest = 10

↓

largest = 25

secondLargest = 15
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

        int largest = int.MinValue;
        int secondLargest = int.MinValue;

        foreach (int number in numbers)
        {
            if (number > largest)
            {
                secondLargest = largest;
                largest = number;
            }
            else if (number > secondLargest && number != largest)
            {
                secondLargest = number;
            }
        }

        Console.WriteLine($"Largest : {largest}");
        Console.WriteLine($"Second Largest : {secondLargest}");
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

largest = 10

secondLargest = MIN
```

---

### Second Element

```
number = 5

largest = 10

secondLargest = 5
```

---

### Third Element

```
number = 25

largest = 25

secondLargest = 10
```

---

### Fourth Element

```
number = 8

No changes
```

---

### Fifth Element

```
number = 15

secondLargest = 15
```

Final

```
Largest = 25

Second Largest = 15
```

---

# Handling Duplicate Values

Suppose

```
[20,20,15,10]
```

Largest

```
20
```

Second Largest

```
15
```

Notice

We ignore duplicate largest values.

This condition is important

```csharp
number != largest
```

Without it,

```
20

20
```

would incorrectly become

```
Largest = 20

Second Largest = 20
```

---

# Edge Cases

## Case 1

```
Input

[5]

Output

No Second Largest
```

---

## Case 2

```
Input

[20,20]

Output

No Second Largest
```

---

## Case 3

```
Input

[-5,-10,-2]

Output

-5

Second Largest = -5? ❌
```

Correct

```
Largest = -2

Second Largest = -5
```

---

## Case 4

```
Input

[100,90]

Output

90
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

# Why Use `int.MinValue`?

Suppose

```
[-5,-10,-20]
```

If

```csharp
largest = 0;
```

Result becomes incorrect.

Using

```csharp
int.MinValue
```

ensures every integer is larger initially.

---

# Interview Questions

## Why use two variables?

One stores the largest value.

The second stores the second largest value.

This allows solving the problem in a single traversal.

---

## Why check

```csharp
number != largest
```

To avoid duplicate largest values becoming the second largest.

---

## Can this be solved in one traversal?

Yes.

Using

```
largest

secondLargest
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

Example

```
Array.Sort(numbers);

Second Largest

numbers[n-2]
```

But

Sorting takes

```
O(n log n)
```

The one-pass solution is better.

---

# Summary

To find the second largest element,

maintain two variables.

Whenever a larger value is found,

move the current largest into secondLargest.

Ignore duplicate largest values.

This solves the problem efficiently in a single traversal.

---

# Key Takeaways

✔ Use two variables.

✔ Initialize with `int.MinValue`.

✔ Ignore duplicate largest values.

✔ One traversal only.

✔ Time Complexity

```
O(n)
```

✔ Space Complexity

```
O(1)
```

---

# Next Problem

## Second Smallest Element in an Array

Topics Covered

- One Traversal Solution

- `for` Loop

- `foreach`

- Dry Run

- Handling Duplicates

- Edge Cases

- Interview Questions

- Time & Space Complexity