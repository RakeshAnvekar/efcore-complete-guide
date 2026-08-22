# Prime Number (C# Interview Notes)

## Problem Statement

Given an integer, determine whether it is a **Prime Number**.

A **Prime Number** is a number that has **exactly two positive
factors**:

1.  1
2.  The number itself

### Example

``` text
Input    Output
------   -------------------
2        Prime Number
7        Prime Number
11       Prime Number
15       Not a Prime Number
21       Not a Prime Number
1        Not a Prime Number
```

------------------------------------------------------------------------

# Important Rules

## Rule 1: Prime numbers have exactly two factors

``` text
2  → 1, 2
3  → 1, 3
5  → 1, 5
7  → 1, 7
```

## Rule 2: More than two factors means NOT Prime

``` text
4 → 1, 2, 4
6 → 1, 2, 3, 6
8 → 1, 2, 4, 8
9 → 1, 3, 9
```

## Rule 3: A Prime Number is greater than 1

``` text
0 → Not Prime
1 → Not Prime
2 → Prime
```

------------------------------------------------------------------------

# Naive Algorithm

1.  Initialize `count = 0`.
2.  Loop from `1` to `number`.
3.  If `number % i == 0`, increment `count`.
4.  If `count == 2`, it is Prime.
5.  Otherwise, it is Not Prime.

------------------------------------------------------------------------

# C# Program (Naive)

``` csharp
using System;

class Program
{
    static void Main()
    {
        int number = 7;
        int count = 0;

        for (int i = 1; i <= number; i++)
        {
            if (number % i == 0)
                count++;
        }

        Console.WriteLine(count == 2 ? "Prime Number" : "Not a Prime Number");
    }
}
```

------------------------------------------------------------------------

# Optimized Interview Approach

Only check divisibility up to **√n** because factors occur in pairs.

Algorithm:

1.  If `number <= 1`, not prime.
2.  Loop from `2` while `i * i <= number`.
3.  If divisible, it is not prime.
4.  Otherwise, it is prime.

------------------------------------------------------------------------

# C# Program (Optimized)

``` csharp
using System;

class Program
{
    static void Main()
    {
        int number = 29;

        if (number <= 1)
        {
            Console.WriteLine("Not a Prime Number");
            return;
        }

        bool isPrime = true;

        for (int i = 2; i * i <= number; i++)
        {
            if (number % i == 0)
            {
                isPrime = false;
                break;
            }
        }

        Console.WriteLine(isPrime ? "Prime Number" : "Not a Prime Number");
    }
}
```
Why i * i <= number?

Let's use 29.

i	i × i
-----------|
2	 4
3	 9
4	 16
5	 25
6	 36
------------------------------------------------------------------------

# Time Complexity

-   Naive: **O(n)**
-   Optimized: **O(√n)**

# Space Complexity

**O(1)**

------------------------------------------------------------------------

# Interview Answer

> A prime number has exactly two positive factors: 1 and itself. To
> optimize, I check divisibility only up to the square root of the
> number because factors always occur in pairs. If no divisor is found,
> the number is prime. The optimized solution runs in **O(√n)** time and
> **O(1)** space.

------------------------------------------------------------------------

# Key Points to Remember

-   Prime numbers have exactly **two factors**.
-   Numbers **≤ 1** are not prime.
-   Naive approach → **O(n)**.
-   Optimized approach → **O(√n)**.
-   Factors occur in pairs.
-   Preferred interview solution: **Check only up to √n**.
