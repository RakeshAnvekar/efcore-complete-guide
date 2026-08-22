# 🚀 C# Find Duplicate Number – HashSet vs Sorting vs Dictionary

> **Difficulty:** ⭐⭐☆☆☆
>
> **Interview Level:** Beginner → Mid-Level .NET Developer
>
> **Key Concept:** There are multiple ways to find duplicate numbers in an array. The most common interview approaches are **HashSet**, **Sorting**, and **Dictionary**. Each has different time and space complexities.

---

# 📚 Table of Contents

- 🎯 Problem Statement
- 🔹 Solution 1 – HashSet
- 🔹 Solution 2 – Sorting
- 🔹 Solution 3 – Dictionary
- 📊 Complexity Comparison
- 📊 HashSet vs Dictionary
- 📊 When to Use Which?
- 🎤 Common Interview Questions
- 📌 Summary
- 🎯 Interview One-Liner

---

# 🎯 Problem Statement

Given an integer array, find the duplicate number.

Example

```text
Input

[1,3,4,2,2]

Output

2
```

Another Example

```text
Input

[5,4,7,8,5]

Output

5
```

---

# 🔹 Solution 1 – Using HashSet ⭐⭐⭐⭐

## What is HashSet?

A **HashSet** stores **only unique values**.

When adding an element,

- If it doesn't exist → Add it.
- If it already exists → Duplicate found.

---

## Algorithm

```
Create HashSet

↓

Read Each Number

↓

Already Exists?

↓

Yes

↓

Duplicate Found

↓

No

↓

Add To HashSet
```

---

## Code

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static int FindDuplicate(int[] nums)
    {
        HashSet<int> set = new HashSet<int>();

        foreach (int num in nums)
        {
            if (set.Contains(num))
                return num;

            set.Add(num);
        }

        return -1;
    }

    static void Main()
    {
        int[] nums = {1,3,4,2,2};

        Console.WriteLine(FindDuplicate(nums));
    }
}
```

---

## Dry Run

Input

```
1 3 4 2 2
```

Execution

```
HashSet = {}

↓

1

↓

Add

↓

{1}

↓

3

↓

Add

↓

{1,3}

↓

4

↓

Add

↓

{1,3,4}

↓

2

↓

Add

↓

{1,2,3,4}

↓

2

↓

Already Exists

↓

Duplicate = 2
```

---

## Complexity

| Time | Space |
|------|-------|
| O(n) | O(n) |

---

## Advantages

✔ Fast

✔ Easy to Write

✔ Best for Interviews

✔ Doesn't Modify Original Array

---

## Disadvantages

❌ Extra Memory Required

---

# 🔹 Solution 2 – Using Sorting ⭐⭐⭐

## Idea

Sort the array.

Duplicate numbers become adjacent.

---

## Algorithm

```
Sort Array

↓

Compare Current

With Next

↓

Equal?

↓

Duplicate Found
```

---

## Code

```csharp
using System;

class Program
{
    static int FindDuplicate(int[] nums)
    {
        Array.Sort(nums);

        for (int i = 0; i < nums.Length - 1; i++)
        {
            if (nums[i] == nums[i + 1])
                return nums[i];
        }

        return -1;
    }

    static void Main()
    {
        int[] nums = {1,3,4,2,2};

        Console.WriteLine(FindDuplicate(nums));
    }
}
```

---

## Dry Run

Original

```
1 3 4 2 2
```

After Sorting

```
1 2 2 3 4
```

Execution

```
1 == 2

No

↓

2 == 2

Yes

↓

Duplicate = 2
```

---

## Complexity

| Time | Space |
|------|-------|
| O(n log n) | O(1)* |

> *`Array.Sort()` modifies the original array.

---

## Advantages

✔ No Extra Data Structure

✔ Easy Logic

---

## Disadvantages

❌ Slower than HashSet

❌ Modifies Original Array

---

# 🔹 Solution 3 – Using Dictionary ⭐⭐⭐⭐

## Idea

Store every number with its frequency.

When frequency becomes greater than 1,

duplicate is found.

---

## Algorithm

```
Dictionary

↓

Read Number

↓

Exists?

↓

Yes

↓

Increase Count

↓

Count > 1 ?

↓

Duplicate Found

↓

No

↓

Add To Dictionary
```

---

## Code

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static int FindDuplicate(int[] nums)
    {
        Dictionary<int,int> dict =
            new Dictionary<int,int>();

        foreach(int num in nums)
        {
            if(dict.ContainsKey(num))
                dict[num]++;
            else
                dict[num]=1;

            if(dict[num] > 1)
                return num;
        }

        return -1;
    }

    static void Main()
    {
        int[] nums = {1,3,4,2,2};

        Console.WriteLine(FindDuplicate(nums));
    }
}
```

---

## Dry Run

```
Dictionary

{}

↓

1

↓

{1=1}

↓

3

↓

{1=1,3=1}

↓

4

↓

{1=1,3=1,4=1}

↓

2

↓

{1=1,3=1,4=1,2=1}

↓

2

↓

{1=1,3=1,4=1,2=2}

↓

Duplicate Found
```

---

## Complexity

| Time | Space |
|------|-------|
| O(n) | O(n) |

---

## Advantages

✔ Stores Frequency

✔ Can Count Multiple Duplicates

✔ Useful for Frequency Problems

---

## Disadvantages

❌ More Memory

❌ More Code than HashSet

---

# 📊 Complexity Comparison

| Approach | Time | Space |
|----------|------|--------|
| HashSet | O(n) | O(n) |
| Sorting | O(n log n) | O(1)* |
| Dictionary | O(n) | O(n) |

---

# 📊 HashSet vs Dictionary

| Feature | HashSet | Dictionary |
|----------|----------|------------|
| Stores | Unique Values | Key-Value Pair |
| Duplicate Detection | Yes | Yes |
| Frequency Count | No | Yes |
| Memory | Less | More |
| Faster | Slightly | Slightly Slower |
| Best Use | Find Duplicate | Count Frequency |

---

# 📊 When Should You Use Which?

| Scenario | Recommended |
|----------|-------------|
| Find One Duplicate | HashSet |
| Count Occurrences | Dictionary |
| Frequency Problems | Dictionary |
| No Extra Collection Allowed | Sorting |
| Preserve Original Order | HashSet |
| Best Interview Solution | HashSet |

---

# 🌍 Real-World Examples

## HashSet

```
Employee IDs

101

102

103

101

↓

Duplicate Employee ID
```

---

## Dictionary

```
Word Count

Apple

Banana

Apple

Orange

Apple

↓

Apple = 3

Banana = 1

Orange = 1
```

---

## Sorting

```
Student Roll Numbers

34

12

21

12

↓

Sort

↓

12

12

21

34

↓

Duplicate Found
```

---

# 🎤 Common Interview Questions

## Why is HashSet faster?

Because HashSet performs lookup using hashing.

Average lookup time is **O(1)**.

---

## Why use Dictionary?

Dictionary is useful when you need both:

- Duplicate detection
- Frequency count

---

## Why is Sorting slower?

Sorting itself takes

```
O(n log n)
```

before checking duplicates.

---

## Which solution is best?

For most interviews,

**HashSet** is considered the best practical solution.

---

## Which solution uses the least memory?

Sorting (if modifying the original array is acceptable).

---

# 📌 Summary

| Requirement | Best Choice |
|-------------|-------------|
| Find Duplicate | HashSet |
| Count Frequency | Dictionary |
| No Extra Memory | Sorting |
| Best Performance | HashSet |
| Interview Friendly | HashSet |

---

# 🎯 Interview One-Liner

> **HashSet is the preferred approach for finding duplicate elements because it provides O(n) time complexity with simple implementation. Dictionary is useful when frequency counting is required, while Sorting is a good option when additional memory cannot be used but has a higher time complexity of O(n log n).**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

**"How many ways can you solve Duplicate Number?"**

Answer in this order:

1. **Brute Force** → O(n²)
2. **Sorting** → O(n log n)
3. **HashSet** → O(n) Time, O(n) Space ✅ (Most Practical)
4. **Dictionary** → O(n) Time, O(n) Space (When frequency is required)
5. **Floyd's Cycle Detection** → O(n) Time, O(1) Space ⭐ (Optimal for the specific constraints of the classic problem)