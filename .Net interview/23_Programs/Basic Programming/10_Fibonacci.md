# Fibonacci Series (C# Interview Notes)

## Problem Statement

Given a number **N**, print the first **N** numbers in the Fibonacci series.

A Fibonacci series is a sequence in which every number is the sum of the previous two numbers.

### Example

```text
Input
N = 10

Output
0 1 1 2 3 5 8 13 21 34
```

---

# Important Rules

## Rule 1: Fibonacci Always Starts With

```text
0 1
```

These are the first two Fibonacci numbers.

---

## Rule 2: Next Number

The next Fibonacci number is calculated as:

```text
Next = First + Second
```

Example

```text
0 1

0 + 1 = 1

1 + 1 = 2

1 + 2 = 3

2 + 3 = 5

3 + 5 = 8
```

---

# Why Does the Loop Start From 3?

This is one of the most common interview questions.

Suppose

```text
N = 5
```

We need to print

```text
Position    Number

1           0

2           1

3           1

4           2

5           3
```

Notice that the **1st** and **2nd** Fibonacci numbers are already known.

So before entering the loop, we print them.

```csharp
Console.Write(first + " " + second + " ");
```

Output

```text
0 1
```

Now we have already printed:

- 1st Fibonacci number
- 2nd Fibonacci number

The loop now has to calculate the **3rd Fibonacci number**.

Therefore the loop starts from

```csharp
for(int i = 3; i <= n; i++)
```

---

# Visual Explanation

```text
Position

1 -----> 0

2 -----> 1

3 -----> 1

4 -----> 2

5 -----> 3

6 -----> 5

7 -----> 8

8 -----> 13
```

Already printed

```text
Position 1

Position 2
```

Loop calculates

```text
Position 3

Position 4

Position 5

...
```

---

# Algorithm

1. Initialize

```text
first = 0

second = 1
```

2. Print first and second.
3. Start the loop from **3** because two numbers are already printed.
4. Calculate

```text
next = first + second
```

5. Print next.
6. Update

```text
first = second

second = next
```

7. Repeat until N numbers are printed.

---

# Dry Run

Suppose

```text
N = 8
```

Initially

```text
first = 0

second = 1
```

Print

```text
0 1
```

---

### Iteration 1 (i = 3)

```text
next = 0 + 1

next = 1

Print 1

first = 1

second = 1
```

Series

```text
0 1 1
```

---

### Iteration 2 (i = 4)

```text
next = 1 + 1

next = 2

Print 2

first = 1

second = 2
```

Series

```text
0 1 1 2
```

---

### Iteration 3 (i = 5)

```text
next = 1 + 2

next = 3

Print 3

first = 2

second = 3
```

Series

```text
0 1 1 2 3
```

---

### Iteration 4 (i = 6)

```text
next = 2 + 3

next = 5

Print 5

first = 3

second = 5
```

Series

```text
0 1 1 2 3 5
```

---

### Iteration 5 (i = 7)

```text
next = 3 + 5

next = 8

Print 8
```

Series

```text
0 1 1 2 3 5 8
```

---

### Iteration 6 (i = 8)

```text
next = 5 + 8

next = 13

Print 13
```

Final Series

```text
0 1 1 2 3 5 8 13
```

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        int n = 10;

        int first = 0;
        int second = 1;
        int next;

        Console.Write(first + " " + second + " ");

        for (int i = 3; i <= n; i++)
        {
            next = first + second;

            Console.Write(next + " ");

            first = second;
            second = next;
        }
    }
}
```

---

# Dry Run Table

| Iteration | first | second | next | Output |
|-----------|------:|-------:|-----:|--------|
|Start|0|1|-|0 1|
|3|0|1|1|0 1 1|
|4|1|1|2|0 1 1 2|
|5|1|2|3|0 1 1 2 3|
|6|2|3|5|0 1 1 2 3 5|
|7|3|5|8|0 1 1 2 3 5 8|
|8|5|8|13|0 1 1 2 3 5 8 13|

---

# Time Complexity

The loop runs once for each remaining Fibonacci number.

Since two numbers are printed before the loop, the loop executes:

```text
N - 2
```

times.

Overall

```text
Time Complexity = O(N)
```

---

# Space Complexity

Only three integer variables are used.

- first
- second
- next

```text
Space Complexity = O(1)
```

---

# Edge Cases

### Case 1

```text
N = 1

Output

0
```

---

### Case 2

```text
N = 2

Output

0 1
```

---

### Case 3

```text
N = 0

Output

Nothing to print.
```

---

# Common Mistakes

## Mistake 1

Using

```csharp
Console.WriteLine(next);
```

Output becomes

```text
0 1 1
2
3
5
8
```

Use

```csharp
Console.Write(next + " ");
```

---

## Mistake 2

Wrong update order

```csharp
first = next;

second = first;
```

Incorrect.

Correct

```csharp
first = second;

second = next;
```

---

## Mistake 3

Starting the loop from

```csharp
i = 1
```

after already printing

```text
0 1
```

This prints extra numbers.

---

# Interview Questions

### Q1. Why does the loop start from 3?

Because the first two Fibonacci numbers (0 and 1) are already printed before entering the loop. The loop begins by calculating the **third** Fibonacci number.

---

### Q2. Why use three variables?

Because each Fibonacci number depends on the previous two numbers.

---

### Q3. Why is the iterative solution preferred?

Because it runs in

```text
Time Complexity = O(N)

Space Complexity = O(1)
```

---

# Interview Answer

> The Fibonacci series starts with **0** and **1**. These two numbers are printed before the loop. Since they represent the first and second Fibonacci numbers, the loop begins at the third position (`i = 3`). In each iteration, the next Fibonacci number is calculated as the sum of the previous two numbers. After printing the new value, the previous numbers are updated for the next iteration. The iterative solution runs in **O(N)** time and **O(1)** space.

---

# Key Points to Remember

- Fibonacci starts with **0 1**
- Next = **First + Second**
- Print the first two numbers before the loop.
- Loop starts from **3** because the first two numbers are already printed.
- Update

```text
first = second

second = next
```

- Time Complexity → **O(N)**
- Space Complexity → **O(1)**
- Preferred interview solution → **Iterative Approach**