# Reverse String (C# Interview Notes)

## Problem Statement

Given a string, reverse all of its characters.

### Example

```text
Input

Hello

Output

olleH
```

Another Example

```text
Input

Rakesh

Output

hsekaR
```

---

# Approach 1 : Using for Loop ⭐⭐⭐⭐⭐ (Recommended)

This is the most common interview solution.

## Algorithm

1. Read the string.
2. Start from the last character.
3. Print each character while moving backward.
4. Continue until the first character.

---

# Dry Run

Input

```text
Hello
```

Characters

```text
Index

0 1 2 3 4

H e l l o
```

Start from

```text
Length - 1 = 4
```

Iteration 1

```text
i = 4

Character = o

Output = o
```

Iteration 2

```text
i = 3

Character = l

Output = ol
```

Iteration 3

```text
i = 2

Character = l

Output = oll
```

Iteration 4

```text
i = 1

Character = e

Output = olle
```

Iteration 5

```text
i = 0

Character = H

Output = olleH
```

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string text = "Hello";

        for (int i = text.Length - 1; i >= 0; i--)
        {
            Console.Write(text[i]);
        }
    }
}
```

Output

```text
olleH
```

---

# Time Complexity

Loop runs once for every character.

```text
O(n)
```

---

# Space Complexity

If only printing

```text
O(1)
```

If storing the reversed string

```text
O(n)
```

---

# Advantages

- Simple
- Easy to understand
- Preferred in interviews

---

# Approach 2 : Using Array.Reverse() ⭐⭐⭐⭐

Convert the string into a character array.

Reverse the array.

Create a new string.

---

# Algorithm

1. Convert string to character array.
2. Call `Array.Reverse()`.
3. Create a new string from the reversed array.

---

# Dry Run

Input

```text
Hello
```

Character Array

```text
H e l l o
```

After

```text
Array.Reverse()
```

```text
o l l e H
```

Output

```text
olleH
```

---

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        string text = "Hello";

        char[] chars = text.ToCharArray();

        Array.Reverse(chars);

        string reverse = new string(chars);

        Console.WriteLine(reverse);
    }
}
```

---

# Time Complexity

`Array.Reverse()` visits each character once.

```text
O(n)
```

---

# Space Complexity

Character array is created.

```text
O(n)
```

---

# Advantages

- Short code
- Easy to read
- Production friendly

---

# Approach 3 : Using Stack ⭐⭐⭐

A stack follows the **LIFO (Last In, First Out)** principle.

The last character pushed is the first character popped.

---

# Stack Visualization

Input

```text
Hello
```

Push Characters

```text
Push H

Push e

Push l

Push l

Push o
```

Stack

```text
Top

o

l

l

e

H
```

Now Pop

```text
o

l

l

e

H
```

Output

```text
olleH
```

---

# Algorithm

1. Create a stack.
2. Push every character into the stack.
3. Pop characters one by one.
4. Print each popped character.

---

# C# Program

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        string text = "Hello";

        Stack<char> stack = new Stack<char>();

        foreach (char c in text)
        {
            stack.Push(c);
        }

        while (stack.Count > 0)
        {
            Console.Write(stack.Pop());
        }
    }
}
```

Output

```text
olleH
```

---

# Dry Run

Input

```text
Hello
```

Push

```text
H

e

l

l

o
```

Pop

```text
o

l

l

e

H
```

Final Output

```text
olleH
```

---

# Time Complexity

Push

```text
O(n)
```

Pop

```text
O(n)
```

Overall

```text
O(n)
```

---

# Space Complexity

Stack stores all characters.

```text
O(n)
```

---

# Comparison

| Approach | Time | Space | Interview | Production |
|----------|------|-------|-----------|------------|
|for Loop|O(n)|O(1)*|⭐⭐⭐⭐⭐|⭐⭐⭐⭐|
|Array.Reverse()|O(n)|O(n)|⭐⭐⭐⭐|⭐⭐⭐⭐⭐|
|Stack|O(n)|O(n)|⭐⭐⭐|⭐⭐⭐|

> **Note:** The `for` loop uses **O(1)** space only if you print the characters directly. If you create a new reversed string, it requires **O(n)** space.

---

# Interview Questions

### Q1. Which approach is preferred in interviews?

**Answer**

The manual **for loop** approach because it demonstrates your understanding of string indexing and iteration.

---

### Q2. Which approach is preferred in production?

**Answer**

`Array.Reverse()` because it is concise, readable, and uses built-in .NET functionality.

---

### Q3. Why does the Stack approach work?

Because a stack follows the **Last In, First Out (LIFO)** principle. The last character inserted is the first character removed, naturally reversing the string.

---

# Interview Answer

> There are several ways to reverse a string in C#. The most common interview approach is using a `for` loop to iterate from the last character to the first, which runs in **O(n)** time and **O(1)** extra space when only printing. Another approach is using `Array.Reverse()`, which is concise and production-friendly but requires **O(n)** extra space for the character array. A third approach uses a `Stack<char>`, leveraging the LIFO principle to reverse the characters, also with **O(n)** time and **O(n)** space.

---

# Key Points to Remember

- **for Loop** → Best interview approach.
- **Array.Reverse()** → Best built-in solution.
- **Stack** → Uses the LIFO principle.
- All three approaches have **O(n)** time complexity.
- `for` loop → **O(1)** space (printing only).
- `Array.Reverse()` → **O(n)** space.
- `Stack` → **O(n)** space.