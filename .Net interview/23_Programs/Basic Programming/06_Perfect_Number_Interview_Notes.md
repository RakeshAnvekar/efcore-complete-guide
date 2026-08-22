# Perfect Number (C# Interview Notes)

## Problem Statement

A **Perfect Number** is a positive integer that is equal to the sum of
all its **proper divisors** (excluding the number itself).

### Examples

    Input Proper Divisors     Sum        Output
  ------- ----------------- ----- --------------------
        6 1,2,3                 6    Perfect Number
       28 1,2,4,7,14           28    Perfect Number
       12 1,2,3,4,6            16  Not Perfect Number

------------------------------------------------------------------------

# Important Rules

## Rule 1: Proper divisors

A proper divisor divides the number exactly and is **less than the
number**.

For 28:

``` text
1, 2, 4, 7, 14
```

Do **not** include 28 itself.

------------------------------------------------------------------------

## Rule 2: Check divisibility

``` text
number % i == 0
```

If true, `i` is a divisor.

------------------------------------------------------------------------

## Rule 3: Add all proper divisors

``` text
sum += i
```

------------------------------------------------------------------------

# Algorithm

1.  Read the number.
2.  Initialize `sum = 0`.
3.  Loop from `1` to `number / 2`.
4.  If `number % i == 0`, add `i` to `sum`.
5.  Compare `sum` with `number`.

------------------------------------------------------------------------

# Dry Run

``` text
number = 28

i=1 -> divisor -> sum=1
i=2 -> divisor -> sum=3
i=3 -> not divisor
i=4 -> divisor -> sum=7
i=5 -> not divisor
i=6 -> not divisor
i=7 -> divisor -> sum=14
...
i=14 -> divisor -> sum=28

28 == 28

Perfect Number
```

------------------------------------------------------------------------

# C# Program

``` csharp
using System;

public class Program
{
    public static void Main()
    {
        int number = 28;
        int sum = 0;

        for (int i = 1; i <= number / 2; i++)
        {
            if (number % i == 0)
            {
                sum += i;
            }
        }

        if (sum == number)
            Console.WriteLine("Perfect Number");
        else
            Console.WriteLine("Not a Perfect Number");
    }
}
```

------------------------------------------------------------------------

# Why Loop Only Until `number / 2`?

A number cannot have a proper divisor greater than half of itself
(except the number itself, which we exclude).

Example:

``` text
28 / 2 = 14
```

No integer greater than 14 (and less than 28) can divide 28 exactly.

------------------------------------------------------------------------

# Time Complexity

The loop runs from `1` to `number / 2`.

**Time Complexity: O(n)**

(Technically O(n/2), which simplifies to O(n).)

------------------------------------------------------------------------

# Space Complexity

Only a few integer variables are used.

**Space Complexity: O(1)**

------------------------------------------------------------------------

# Edge Cases

-   1 -\> Not a Perfect Number
-   6 -\> Perfect Number
-   28 -\> Perfect Number
-   Negative numbers -\> Not Perfect Numbers

------------------------------------------------------------------------

# Common Interview Follow-up

### Can you optimize it?

Yes. Instead of checking up to `number / 2`, iterate only up to
`sqrt(number)` and add divisor pairs.

That reduces the time complexity to **O(√n)**.

------------------------------------------------------------------------

# Interview Answer

> I iterate through all proper divisors of the number, add them to a
> running sum, and compare the sum with the original number. The basic
> approach checks divisors up to `number / 2`, giving a time complexity
> of **O(n)** and a space complexity of **O(1)**. An optimized approach
> checks only up to the square root of the number, reducing the time
> complexity to **O(√n)**.

------------------------------------------------------------------------

# Key Takeaways

-   Proper divisors exclude the number itself.
-   Use `%` to check divisibility.
-   Sum all proper divisors.
-   Compare the sum with the original number.
-   Basic Time Complexity: **O(n)**
-   Optimized Time Complexity: **O(√n)**
-   Space Complexity: **O(1)**
