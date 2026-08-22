# HashSet in C#

> **Course:** Data Structures & Algorithms using C#

**Topic:** HashSet Collection

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is HashSet?
2. Why Do We Need HashSet?
3. Characteristics of HashSet
4. Creating a HashSet
5. Adding Elements
6. Removing Elements
7. Searching Elements
8. Removing Duplicates from an Array
9. Common Methods
10. Time Complexity
11. HashSet vs List
12. Advantages
13. Disadvantages
14. Interview Questions
15. Summary
16. Key Takeaways

---

# What is HashSet?

A **HashSet** is a collection that stores **only unique elements**.

If you try to add duplicate values, HashSet automatically ignores them.

Namespace

```csharp
using System.Collections.Generic;
```

---

# Why Do We Need HashSet?

Suppose you have the following array

```text
4 2 1 4 2 5 6 5
```

There are duplicate values.

Expected Output

```text
4 2 1 5 6
```

Instead of writing complex logic,

HashSet automatically removes duplicate values.

---

# Characteristics of HashSet

✔ Stores only unique elements

✔ Does not allow duplicates

✔ Fast searching

✔ Fast insertion

✔ Does not maintain insertion order

✔ Uses Hashing internally

---

# Creating a HashSet

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        HashSet<int> numbers = new HashSet<int>();
    }
}
```

---

# Adding Elements

```csharp
HashSet<int> numbers = new HashSet<int>();

numbers.Add(10);
numbers.Add(20);
numbers.Add(30);
numbers.Add(20);
numbers.Add(10);

foreach(int num in numbers)
{
    Console.Write(num + " ");
}
```

Output

```text
10 20 30
```

Notice

Duplicate values are ignored automatically.

---

# How Add() Works

Initially

```
HashSet

{}
```

Add

```
10
```

```
{10}
```

Add

```
20
```

```
{10,20}
```

Add

```
30
```

```
{10,20,30}
```

Add

```
20
```

Already exists.

Ignored.

---

# Searching Elements

```csharp
HashSet<int> numbers = new HashSet<int>()
{
    10,
    20,
    30
};

Console.WriteLine(numbers.Contains(20));
```

Output

```
True
```

---

# Removing Elements

```csharp
numbers.Remove(20);
```

Output

```
10

30
```

---

# Count Property

```csharp
Console.WriteLine(numbers.Count);
```

Output

```
3
```

---

# Removing Duplicates from an Array

Input

```
4 2 1 4 2 5 6 5
```

Code

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        int[] numbers = {4,2,1,4,2,5,6,5};

        HashSet<int> uniqueNumbers = new HashSet<int>();

        foreach(int num in numbers)
        {
            uniqueNumbers.Add(num);
        }

        Console.WriteLine("Unique Elements:");

        foreach(int num in uniqueNumbers)
        {
            Console.Write(num + " ");
        }
    }
}
```

Output

```
4 2 1 5 6
```

---

# Dry Run

Input

```
4 2 1 4 2 5 6 5
```

Initially

```
{}
```

Add

```
4
```

```
{4}
```

Add

```
2
```

```
{4,2}
```

Add

```
1
```

```
{4,2,1}
```

Add

```
4
```

Already exists.

Ignored.

```
{4,2,1}
```

Add

```
2
```

Ignored.

```
{4,2,1}
```

Add

```
5
```

```
{4,2,1,5}
```

Add

```
6
```

```
{4,2,1,5,6}
```

Add

```
5
```

Ignored.

Final Result

```
4 2 1 5 6
```

---

# Important Methods

## Add()

Adds an element.

```csharp
numbers.Add(10);
```

---

## Remove()

Removes an element.

```csharp
numbers.Remove(10);
```

---

## Contains()

Checks whether an element exists.

```csharp
numbers.Contains(20);
```

Returns

```
True

or

False
```

---

## Clear()

Removes all elements.

```csharp
numbers.Clear();
```

---

## Count

Returns the number of elements.

```csharp
Console.WriteLine(numbers.Count);
```

---

# Time Complexity

| Operation | Complexity |
|------------|------------|
| Add | O(1) Average |
| Remove | O(1) Average |
| Contains | O(1) Average |
| Search | O(1) Average |

---

# HashSet vs List

| Feature | HashSet | List |
|----------|----------|------|
| Allows Duplicates | ❌ No | ✅ Yes |
| Ordering | ❌ No Guarantee | ✅ Preserved |
| Search | O(1) | O(n) |
| Add | O(1) | O(1) |
| Remove Duplicate Values | Excellent | Manual Logic Required |

---

# Advantages

✔ Automatically removes duplicates

✔ Very fast searching

✔ Fast insertion

✔ Efficient for membership checks

✔ Easy to use

---

# Disadvantages

✖ Does not maintain insertion order

✖ Cannot access elements using an index

```csharp
numbers[0]
```

Not allowed.

---

# When Should You Use HashSet?

Use HashSet when

- You need unique values.
- Fast searching is important.
- Removing duplicates.
- Membership checking.
- Set operations like Union, Intersect, and Except.

---

# Interview Questions

## What is HashSet?

HashSet is a collection that stores only unique elements using hashing.

---

## Does HashSet allow duplicates?

No.

Duplicate values are ignored automatically.

---

## Does HashSet maintain insertion order?

No.

HashSet does not guarantee insertion order.

---

## Why is HashSet faster than List for searching?

HashSet uses a hash table internally, allowing average O(1) lookup time.

---

## Can we access elements by index?

No.

HashSet does not support indexing.

---

## What is the time complexity of Add()?

```
O(1)
```

Average case.

---

## Which is better for removing duplicates?

HashSet.

It removes duplicates automatically without extra comparison logic.

---

# Summary

HashSet is one of the most useful collections in C# for storing unique values.

It automatically removes duplicates and provides very fast insertion, deletion, and searching operations.

It is commonly used in coding interviews to remove duplicate elements from arrays and to perform efficient membership checks.

---

# Key Takeaways

✔ HashSet stores only unique values.

✔ Duplicate values are ignored automatically.

✔ Searching is O(1) on average.

✔ Best collection for removing duplicates.

✔ Does not maintain insertion order.

✔ Does not support indexing.

✔ Common interview collection in C#.

---

# Next Topic

## Dictionary<TKey, TValue>

Topics Covered

- What is Dictionary?
- Key-Value Pair
- Add
- Update
- Remove
- ContainsKey
- TryGetValue
- Dictionary vs HashSet
- Dictionary vs Hashtable
- Interview Questions
- Time Complexity
```