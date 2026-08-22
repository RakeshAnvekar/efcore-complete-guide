# Armstrong Number (C# Interview Notes)

## Problem Statement

Given an integer, determine whether it is an **Armstrong Number**.

An Armstrong Number is a number that is equal to the sum of each of its
digits raised to the power of the total number of digits.

### Examples

    Input Calculation                    Output
  ------- -------------------------- ---------------
      153 1³ + 5³ + 3³ = 153            Armstrong
      370 3³ + 7³ + 0³ = 370            Armstrong
     9474 9⁴ + 4⁴ + 7⁴ + 4⁴ = 9474      Armstrong
      123 1³ + 2³ + 3³ = 36           Not Armstrong

------------------------------------------------------------------------

# Important Rules

## Rule 1: Count the number of digits

``` text
153 -> 3 digits
9474 -> 4 digits
```

## Rule 2: Extract the last digit

``` text
digit = number % 10
```

## Rule 3: Raise each digit to the power of the digit count

``` text
sum += digit^count
```

Example:

``` text
153

1³ + 5³ + 3³

1 + 125 + 27 = 153
```

## Rule 4: Remove the last digit

``` text
number = number / 10
```

------------------------------------------------------------------------

# Algorithm

1.  Store the original number.
2.  Count the digits.
3.  Reset the number to the original value.
4.  Extract each digit using `% 10`.
5.  Add `digit^count` to `sum`.
6.  Remove the last digit using `/ 10`.
7.  Compare `sum` with the original number.

------------------------------------------------------------------------

# Dry Run

``` text
number = 153
count = 3

digit = 3
sum = 3³ = 27

digit = 5
sum = 27 + 5³ = 152

digit = 1
sum = 152 + 1³ = 153

Compare

153 == 153

Armstrong Number
```

------------------------------------------------------------------------

# C# Program

``` csharp
using System;

public class Program
{
    public static void Main()
    {
        int number = 153;
        int originalNumber = number;

        int count = 0;
        while (number != 0)
        {
            count++;
            number /= 10;
        }

        number = originalNumber;
        int sum = 0;

        while (number != 0)
        {
            int digit = number % 10;
            sum += (int)Math.Pow(digit, count);
            number /= 10;
        }

        if (sum == originalNumber)
            Console.WriteLine("Armstrong Number");
        else
            Console.WriteLine("Not an Armstrong Number");
    }
}
```

------------------------------------------------------------------------

# Time Complexity

Let **n** be the number of digits.

-   First loop counts digits → O(n)
-   Second loop computes the sum → O(n)

Overall:

**Time Complexity: O(n)**

------------------------------------------------------------------------

# Space Complexity

Only a few integer variables are used.

**Space Complexity: O(1)**

------------------------------------------------------------------------

# Edge Cases

-   0 → Armstrong
-   1 → Armstrong
-   Single-digit numbers are Armstrong numbers.
-   Large numbers work as long as they fit in the data type.

------------------------------------------------------------------------

# Common Interview Follow-up Questions

### Why are there two loops?

-   First loop counts the digits.
-   Second loop calculates the Armstrong sum.

### Why use `Math.Pow()`?

Because each digit must be raised to the power of the total number of
digits.

### Can it be done in one loop?

Not easily, because you must know the digit count before calculating the
powers.

------------------------------------------------------------------------

# Interview Answer

> I first count the number of digits, then traverse the number again.
> For each digit, I calculate `digit^count` and add it to a running sum.
> Finally, I compare the sum with the original number. The algorithm
> scans the digits twice, so the time complexity is **O(n)** and the
> extra space used is **O(1)**.

------------------------------------------------------------------------

# Key Takeaways

-   Count digits first.
-   Use `% 10` to extract digits.
-   Use `/ 10` to remove digits.
-   Add `digit^count` to the sum.
-   Compare the sum with the original number.
-   Time Complexity: **O(n)**
-   Space Complexity: **O(1)**
