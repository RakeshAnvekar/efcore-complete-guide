# Character Frequency in C

## Problem Statement

Given a string, count the frequency of each character.

Example input:

``` text
RakeshRa
```

Example output:

``` text
R : 2
a : 2
k : 1
e : 1
s : 1
h : 1
```

------------------------------------------------------------------------

## Algorithm

1.  Create a Dictionary\<char,int\>.
2.  Traverse each character.
3.  If it exists, increment its count.
4.  Otherwise, add it with value 1.
5.  Print the dictionary.

------------------------------------------------------------------------

## C# Code

``` csharp
using System;
using System.Collections.Generic;

public class HelloWorld
{
    public static void Main()
    {
        string str = "Ra kesh Ra";

        Dictionary<char, int> dic = new Dictionary<char, int>();

        foreach (char c in str)
        {
            // Ignore spaces
            if (c == ' ')
                continue;

            if (dic.ContainsKey(c))
            {
                dic[c]++;
            }
            else
            {
                dic[c] = 1;
            }
        }

        foreach (var item in dic)
        {
            Console.WriteLine($"{item.Key} : {item.Value}");
        }
    }
}
```

------------------------------------------------------------------------

## Common Mistakes

### Printing inside the first foreach loop

Print only after building the dictionary.

### Using incorrect property names

Wrong:

``` csharp
data.key
daya.value
```

Correct:

``` csharp
data.Key
data.Value
```

------------------------------------------------------------------------

## Dry Run

Input:

banana

Dictionary:

b -\> 1

a -\> 3

n -\> 2

------------------------------------------------------------------------

## Complexity

Time: O(n)

Space: O(k)

Where k is the number of unique characters.

------------------------------------------------------------------------

## Interview Questions

1.  Why use Dictionary?
2.  Can it be solved without Dictionary?
3.  How do you ignore spaces?
4.  How do you make it case insensitive?

------------------------------------------------------------------------

## Key Takeaways

-   Dictionary gives O(1) average lookup.
-   Overall complexity is O(n).
-   Print after processing the string.
-   Use Key and Value properties correctly.
