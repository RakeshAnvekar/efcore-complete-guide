# Reverse a Number (C# Interview Notes)

## Problem Statement

Given an integer, reverse its digits without converting the number into
a string.

### Example

    Input   Output
  ------- --------
    12345    54321
     9070      709
     -123     -321

------------------------------------------------------------------------

# Important Rules

## Rule 1: `% 10` gives the last digit

``` text
1234 % 10 = 4
567 % 10 = 7
98 % 10 = 8
```

**Remember:** `number % 10` always returns the **last digit**.

------------------------------------------------------------------------

## Rule 2: `/ 10` removes the last digit

``` text
1234 / 10 = 123
567 / 10 = 56
98 / 10 = 9
```

**Remember:** Integer division removes the last digit.

------------------------------------------------------------------------

## Rule 3: `reverse * 10`

Multiply the current reversed number by 10 before adding the next digit.

Example:

``` text
reverse = 43
digit = 2

43 * 10 = 430
430 + 2 = 432
```

------------------------------------------------------------------------

# Algorithm

1.  Initialize `reverse = 0`.
2.  Repeat while `number != 0`:
    -   Extract the last digit using `% 10`.
    -   Append it to `reverse` using `reverse = reverse * 10 + digit`.
    -   Remove the last digit using `/ 10`.
3.  Print `reverse`.

------------------------------------------------------------------------

# Dry Run

``` text
number = 1234
reverse = 0

Iteration 1
digit = 1234 % 10 = 4
reverse = 0 * 10 + 4 = 4
number = 1234 / 10 = 123

Iteration 2
digit = 123 % 10 = 3
reverse = 4 * 10 + 3 = 43
number = 123 / 10 = 12

Iteration 3
digit = 12 % 10 = 2
reverse = 43 * 10 + 2 = 432
number = 12 / 10 = 1

Iteration 4
digit = 1 % 10 = 1
reverse = 432 * 10 + 1 = 4321
number = 1 / 10 = 0

Output = 4321
```

------------------------------------------------------------------------

# C# Program

``` csharp
using System;

class Program
{
    static void Main()
    {
        int number = 12345;
        int reverse = 0;

        while (number != 0)
        {
            int digit = number % 10;
            reverse = reverse * 10 + digit;
            number = number / 10;
        }

        Console.WriteLine($"Reversed Number: {reverse}");
    }
}
```

------------------------------------------------------------------------

# Time Complexity

The loop runs once for each digit.

If the number has **n digits**, the loop executes **n** times.

**Time Complexity: O(n)**

Example:

``` text
12345

1234

123

12

1

0
```

5 digits → 5 iterations.

------------------------------------------------------------------------

# Space Complexity

Only three variables are used:

-   number
-   reverse
-   digit

No additional array, list, or string is created.

**Space Complexity: O(1)**

------------------------------------------------------------------------

# Interview Answer

> I repeatedly extract the last digit using the modulo operator
> (`% 10`), append it to the reversed number by multiplying the current
> result by 10 and adding the extracted digit, and then remove the last
> digit from the original number using integer division (`/ 10`). This
> continues until the number becomes zero. The algorithm processes each
> digit exactly once, so the time complexity is **O(n)** where **n** is
> the number of digits, and the space complexity is **O(1)** because
> only a constant amount of extra memory is used.

------------------------------------------------------------------------

# Key Points to Remember

-   `% 10` → Get the last digit.
-   `/ 10` → Remove the last digit.
-   `reverse * 10` → Shift digits left before appending the next digit.
-   Time Complexity → **O(n)**
-   Space Complexity → **O(1)**
-   Preferred interview solution: Mathematical approach (without using
    strings).
