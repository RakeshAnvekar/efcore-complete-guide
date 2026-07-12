# Print Prime Numbers Between N1 and N2 (C# Interview Notes)

## Problem Statement

Given two integers **N1** and **N2**, print all prime numbers between
them (inclusive).

### Example

``` text
Input
N1 = 10
N2 = 30

Output
11
13
17
19
23
29
```

------------------------------------------------------------------------

## Algorithm

1.  Loop from `N1` to `N2`.
2.  Skip numbers less than or equal to 1.
3.  Assume the current number is prime.
4.  Check divisibility from `2` while `i * i <= number`.
5.  If divisible, mark it as not prime.
6.  If still prime, print it.

------------------------------------------------------------------------

## C# Program

``` csharp
using System;

class Program
{
    static void Main()
    {
        int n1 = 10;
        int n2 = 30;

        for (int number = n1; number <= n2; number++)
        {
            if (number <= 1)
                continue;

            bool isPrime = true;

            for (int i = 2; i * i <= number; i++)
            {
                if (number % i == 0)
                {
                    isPrime = false;
                    break;
                }
            }

            if (isPrime)
                Console.WriteLine(number);
        }
    }
}
```

------------------------------------------------------------------------

## Dry Run

``` text
N1 = 10
N2 = 15

10 -> Not Prime
11 -> Prime
12 -> Not Prime
13 -> Prime
14 -> Not Prime
15 -> Not Prime

Output
11
13
```

------------------------------------------------------------------------

## Time Complexity

`O((N2 - N1 + 1) × √N)`

## Space Complexity

`O(1)`

------------------------------------------------------------------------

## Key Points

-   Outer loop traverses the range.
-   Inner loop checks whether the current number is prime.
-   Use `i * i <= number` for the optimized solution.
-   Break immediately after finding a divisor.
