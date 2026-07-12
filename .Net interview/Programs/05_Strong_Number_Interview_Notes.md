# Strong Number (C# Interview Notes)

## Problem Statement

A **Strong Number** is a number whose sum of the factorials of its
digits is equal to the original number.

Example: - 145 = 1! + 4! + 5! = 145 ✔ - 123 = 1! + 2! + 3! = 9 ✘

------------------------------------------------------------------------

## Important Rules

1.  Store the original number.
2.  Extract the last digit using `% 10`.
3.  Find the factorial of the digit.
4.  Add the factorial to the running sum.
5.  Remove the last digit using `/ 10`.
6.  Compare the sum with the original number.

------------------------------------------------------------------------

## Dry Run

145

5! = 120 → sum = 120

4! = 24 → sum = 144

1! = 1 → sum = 145

145 == 145 → Strong Number

------------------------------------------------------------------------

## C# Program

``` csharp
using System;

public class Program
{
    static int Factorial(int n)
    {
        int fact = 1;

        for(int i=1;i<=n;i++)
        {
            fact *= i;
        }

        return fact;
    }

    public static void Main()
    {
        int number = 145;
        int original = number;
        int sum = 0;

        while(number != 0)
        {
            int digit = number % 10;
            sum += Factorial(digit);
            number /= 10;
        }

        if(sum == original)
            Console.WriteLine("Strong Number");
        else
            Console.WriteLine("Not a Strong Number");
    }
}
```

------------------------------------------------------------------------

## Time Complexity

Outer loop runs once per digit = O(n)

Factorial loop runs at most 9 times (constant) = O(1)

Overall Time Complexity = **O(n)**

------------------------------------------------------------------------

## Space Complexity

Only a few integer variables are used.

**O(1)**

------------------------------------------------------------------------

## Edge Cases

-   1 -\> Strong Number
-   2 -\> Strong Number
-   0 -\> Not Strong Number
-   Negative numbers -\> Not Strong Number

------------------------------------------------------------------------

## Interview Answer

Extract each digit using modulo, compute its factorial, add it to a
running sum, and compare the sum with the original number. Since each
digit is processed once and factorial computation is constant time for a
digit, the overall time complexity is O(n) and the space complexity is
O(1).

------------------------------------------------------------------------

## Key Takeaways

-   `% 10` -\> Get last digit
-   `/ 10` -\> Remove last digit
-   Calculate factorial of each digit
-   Compare factorial sum with original number
-   Time Complexity: O(n)
-   Space Complexity: O(1)
