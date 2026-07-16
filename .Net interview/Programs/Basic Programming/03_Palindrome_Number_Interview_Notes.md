# Check Palindrome Number (C# Interview Notes)

## Problem Statement

Given an integer, determine whether it is a palindrome. A palindrome
reads the same forward and backward.

### Examples

    Input Output
  ------- ----------------
      121 Palindrome
    12321 Palindrome
     1234 Not Palindrome
     1221 Palindrome

------------------------------------------------------------------------

## Important Rules

### Rule 1: Keep the original number

``` csharp
int originalNumber = number;
```

### Rule 2: Reverse the number

``` text
digit = number % 10
reverse = reverse * 10 + digit
number = number / 10
```

### Rule 3: Compare

``` text
originalNumber == reverse
```

------------------------------------------------------------------------

## Algorithm

1.  Store the original number.
2.  Reverse the number.
3.  Compare the original with the reversed value.
4.  If equal -\> Palindrome, otherwise Not Palindrome.

------------------------------------------------------------------------

## Dry Run

``` text
number = 121
original = 121
reverse = 0

digit = 1 -> reverse = 1 -> number = 12
digit = 2 -> reverse = 12 -> number = 1
digit = 1 -> reverse = 121 -> number = 0

121 == 121 -> Palindrome
```

------------------------------------------------------------------------

## C# Program

``` csharp
using System;

public class Program
{
    public static void Main()
    {
        int number = 121;
        int originalNumber = number;
        int reverse = 0;

        while (number != 0)
        {
            int digit = number % 10;
            reverse = reverse * 10 + digit;
            number /= 10;
        }

        if (originalNumber == reverse)
            Console.WriteLine("Palindrome Number");
        else
            Console.WriteLine("Not a Palindrome Number");
    }
}
```

------------------------------------------------------------------------

## Time Complexity

The loop runs once for each digit.

**Time Complexity: O(n)**

where **n** is the number of digits.

------------------------------------------------------------------------

## Space Complexity

Only a few integer variables are used.

**Space Complexity: O(1)**

------------------------------------------------------------------------

## Edge Cases

-   0 -\> Palindrome
-   7 -\> Palindrome
-   10 -\> Not Palindrome
-   -121 -\> Generally considered Not Palindrome

------------------------------------------------------------------------

## Interview Answer

> I store the original number, reverse the digits using modulo and
> integer division, and compare the reversed value with the original
> number. If both are equal, it is a palindrome. The algorithm processes
> each digit once, so the time complexity is O(n), and since only a few
> variables are used, the space complexity is O(1).

------------------------------------------------------------------------

## Key Takeaways

-   Store the original number.
-   Reverse using `% 10` and `/ 10`.
-   Compare original and reversed values.
-   Time Complexity: O(n)
-   Space Complexity: O(1)
