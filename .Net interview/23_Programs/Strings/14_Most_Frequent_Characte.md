# Most Frequent Character in C

## Problem Statement

Given a string, find the character(s) that appear the maximum number of
times.

**Example**

Input:

``` text
RakeshRa
```

Output:

``` text
R : 2
a : 2
```

------------------------------------------------------------------------

# Algorithm

1.  Create a `Dictionary<char,int>`.
2.  Traverse the string and count the frequency of each character.
3.  Traverse the dictionary to find the maximum frequency.
4.  Traverse the dictionary again and print all characters whose
    frequency equals the maximum.

------------------------------------------------------------------------

# C# Solution

``` csharp
using System;
using System.Collections.Generic;

public class HelloWorld
{
    public static void Main(string[] args)
    {
        string str = "RakeshRa";

        Dictionary<char, int> dic = new Dictionary<char, int>();

        foreach (char c in str)
        {
            if (dic.ContainsKey(c))
                dic[c]++;
            else
                dic[c] = 1;
        }

        int max = 0;

        foreach (var data in dic)
        {
            if (data.Value > max)
            {
                max = data.Value;
            }
        }

        Console.WriteLine("Most Frequent Character(s):");

        foreach (var data in dic)
        {
            if (data.Value == max)
            {
                Console.WriteLine($"{data.Key} : {data.Value}");
            }
        }
    }
}
```

------------------------------------------------------------------------

# Common Mistakes

## 1. Using `Value` instead of `data.Value`

Wrong:

``` csharp
max = Value;
```

Correct:

``` csharp
max = data.Value;
```

------------------------------------------------------------------------

## 2. Printing inside the loop that finds the maximum

Wrong:

``` csharp
foreach(var data in dic)
{
    if(data.Value > max)
        max = data.Value;

    foreach(var item in dic)
    {
        ...
    }
}
```

Correct approach:

-   First loop → Find the maximum frequency.
-   Second loop → Print the character(s) with that frequency.

------------------------------------------------------------------------

# Dry Run

Input:

``` text
RakeshRa
```

Dictionary:

  Character     Frequency
  ----------- -----------
  R                     2
  a                     2
  k                     1
  e                     1
  s                     1
  h                     1

Maximum Frequency = **2**

Output:

``` text
R : 2
a : 2
```

------------------------------------------------------------------------

# Complexity

-   **Time Complexity:** O(n)
-   **Space Complexity:** O(k)

Where: - `n` = length of the string - `k` = number of unique characters

------------------------------------------------------------------------

# Interview Questions

### Can multiple characters be the most frequent?

Yes. Print every character whose frequency equals the maximum.

### Can this be solved without a Dictionary?

Yes.

-   Nested loops → O(n²)
-   Fixed-size array (ASCII) → O(n)

------------------------------------------------------------------------

# Key Takeaways

-   Dictionary is the preferred interview solution.
-   Use two passes:
    1.  Find the maximum frequency.
    2.  Print all matching characters.
-   Always mention time and space complexity.
