# Count Digits in a Number (C# Interview Notes)

## Problem Statement

Given an integer, count the total number of digits without converting the
number into a string.

### Example

```text
Input    Output
------   ------
12345    5
987      3
7        1
1000     4
```

------------------------------------------------------------------------

# Important Rules

## Rule 1: `/ 10` Removes the Last Digit

```text
12345 / 10 = 1234

1234 / 10 = 123

123 / 10 = 12

12 / 10 = 1

1 / 10 = 0
```

Each division removes **one digit**.

------------------------------------------------------------------------

## Rule 2: Count Every Division

Every time we divide by 10, one digit disappears.

So we increment the counter.

```text
count++
```

------------------------------------------------------------------------

# Algorithm

1. Initialize `count = 0`.
2. Repeat while `number != 0`
    - Divide the number by 10.
    - Increment `count`.
3. Print `count`.

------------------------------------------------------------------------

# Dry Run

```text
number = 12345

count = 0

Iteration 1

number = 12345 / 10

number = 1234

count = 1

---------------------

Iteration 2

number = 1234 / 10

number = 123

count = 2

---------------------

Iteration 3

number = 123 / 10

number = 12

count = 3

---------------------

Iteration 4

number = 12 / 10

number = 1

count = 4

---------------------

Iteration 5

number = 1 / 10

number = 0

count = 5

Loop Ends

Output = 5
```

------------------------------------------------------------------------

# Dry Run Table

| Iteration | Number Before | Number After | Count |
|-----------|--------------:|-------------:|------:|
|1|12345|1234|1|
|2|1234|123|2|
|3|123|12|3|
|4|12|1|4|
|5|1|0|5|

------------------------------------------------------------------------

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        int number = 12345;
        int count = 0;

        while (number != 0)
        {
            number = number / 10;
            count++;
        }

        Console.WriteLine("Number of Digits = " + count);
    }
}
```

------------------------------------------------------------------------

# Edge Cases

## Case 1

```text
Input = 7

Output = 1
```

------------------------------------------------------------------------

## Case 2

```text
Input = 1000

Output = 4
```

------------------------------------------------------------------------

## Case 3

```text
Input = 0
```

The above program prints

```text
0
```

But mathematically,

```text
0
```

has **1 digit**.

Handle it separately.

```csharp
if (number == 0)
{
    Console.WriteLine(1);
    return;
}
```

------------------------------------------------------------------------

## Case 4

Negative Numbers

```text
Input

-12345
```

Convert to positive.

```csharp
number = Math.Abs(number);
```

Output

```text
5
```

------------------------------------------------------------------------

# Alternative Approach (Using String)

```csharp
using System;

class Program
{
    static void Main()
    {
        int number = 12345;

        int count = number.ToString().Length;

        Console.WriteLine(count);
    }
}
```

### Interview Note

This approach is easy but **not preferred** unless the interviewer allows string conversion.

------------------------------------------------------------------------

# Time Complexity

The loop executes once for each digit.

If the number has **d digits**, the loop runs **d** times.

```text
Time Complexity = O(d)
```

Since:

```text
d = log₁₀(n)
```

it is commonly written as:

```text
Time Complexity = O(log n)
```

------------------------------------------------------------------------

# Space Complexity

Only two variables are used.

- number
- count

No extra array, list, or string is created.

```text
Space Complexity = O(1)
```

------------------------------------------------------------------------

# Common Mistakes

## Mistake 1

Printing outside `Main()`.

Wrong

```csharp
}
Console.WriteLine(count);
```

Correct

```csharp
Console.WriteLine(count);
}
```

------------------------------------------------------------------------

## Mistake 2

Not handling zero.

```text
Input = 0
```

Loop never executes.

Always check:

```csharp
if (number == 0)
{
    Console.WriteLine(1);
    return;
}
```

------------------------------------------------------------------------

## Mistake 3

Not handling negative numbers.

Use

```csharp
number = Math.Abs(number);
```

------------------------------------------------------------------------

# Interview Questions

### Q1. Why does `/ 10` reduce one digit?

Because integer division removes the last digit.

Example

```text
12345

↓

1234

↓

123

↓

12

↓

1

↓

0
```

------------------------------------------------------------------------

### Q2. Why is the time complexity O(log n)?

Each division by 10 reduces the number by one digit.

The number of digits in a number is approximately **log₁₀(n)**.

------------------------------------------------------------------------

### Q3. Can we solve it without a loop?

Yes.

```csharp
number.ToString().Length
```

or

```csharp
(int)Math.Log10(number) + 1
```

But interviewers usually prefer the mathematical approach using `/ 10`.

------------------------------------------------------------------------

# Interview Answer

> I repeatedly divide the number by 10 until it becomes zero. Every division removes one digit, so I increment a counter after each division. The total number of iterations equals the number of digits. Therefore, the algorithm runs in **O(log n)** time and **O(1)** space without using any extra memory or string conversion.

------------------------------------------------------------------------

# Key Points to Remember

- `/ 10` → Removes the last digit.
- `count++` → Counts one digit removed.
- Stop when `number == 0`.
- Handle `0` separately.
- Use `Math.Abs()` for negative numbers.
- Time Complexity → **O(log n)**
- Space Complexity → **O(1)**
- Preferred interview solution: **Mathematical approach (without strings).**