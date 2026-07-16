# Factorial (C# Interview Notes)

## Problem Statement

Given a non-negative integer, find its **factorial**.

The factorial of a number is the product of all positive integers from **1** to the given number.

### Example

```text
Input    Output
-----    ------
5        120
4        24
3        6
2        2
1        1
0        1
```

---

# Important Rules

## Rule 1: Factorial Formula

```text
n! = n × (n-1) × (n-2) × ... × 2 × 1
```

Examples

```text
5! = 5 × 4 × 3 × 2 × 1 = 120

4! = 4 × 3 × 2 × 1 = 24

3! = 3 × 2 × 1 = 6
```

---

## Rule 2: 0! = 1

One of the most common interview questions.

```text
0! = 1
```

This is defined mathematically.

---

## Rule 3: Factorial is defined only for non-negative integers

```text
5  → Valid

0  → Valid

-3 → Invalid
```

---

# Algorithm

1. Read the number.
2. Initialize `fact = 1`.
3. Loop from `1` to `number`.
4. Multiply the current factorial by the loop variable.
5. Print the factorial.

---

# Dry Run

Suppose

```text
number = 5
```

Initially

```text
fact = 1
```

### Iteration 1

```text
i = 1

fact = 1 × 1 = 1
```

---

### Iteration 2

```text
i = 2

fact = 1 × 2 = 2
```

---

### Iteration 3

```text
i = 3

fact = 2 × 3 = 6
```

---

### Iteration 4

```text
i = 4

fact = 6 × 4 = 24
```

---

### Iteration 5

```text
i = 5

fact = 24 × 5 = 120
```

Loop ends.

Output

```text
120
```

---

# C# Program (Iterative)

```csharp
using System;

class Program
{
    static void Main()
    {
        int number = 5;
        int fact = 1;

        for (int i = 1; i <= number; i++)
        {
            fact *= i;
        }

        Console.WriteLine("Factorial = " + fact);
    }
}
```

---

# Dry Run Table

| i | fact before | fact after |
|---|------------:|-----------:|
|1|1|1|
|2|1|2|
|3|2|6|
|4|6|24|
|5|24|120|

Output

```text
120
```

---

# Recursive Solution

## Logic

```text
5!

↓

5 × 4!

↓

5 × 4 × 3!

↓

5 × 4 × 3 × 2!

↓

5 × 4 × 3 × 2 × 1!

↓

120
```

### C# Program

```csharp
using System;

class Program
{
    static int Factorial(int n)
    {
        if (n == 0 || n == 1)
            return 1;

        return n * Factorial(n - 1);
    }

    static void Main()
    {
        int number = 5;

        Console.WriteLine(Factorial(number));
    }
}
```

---

# Time Complexity

The loop executes once for every number from **1 to n**.

```text
Time Complexity = O(n)
```

Example

```text
5

4

3

2

1
```

Five iterations.

---

# Space Complexity

## Iterative

Uses only

- number
- fact
- i

```text
Space Complexity = O(1)
```

---

## Recursive

Each function call is stored in the call stack.

```text
Space Complexity = O(n)
```

---

# Edge Cases

### Case 1

```text
Input = 0

Output = 1
```

---

### Case 2

```text
Input = 1

Output = 1
```

---

### Case 3

```text
Input = -5

Output

Factorial is not defined.
```

---

# Common Mistakes

## Mistake 1

Starting factorial with

```csharp
int fact = 0;
```

Wrong

```text
0 × anything = 0
```

Always initialize

```csharp
int fact = 1;
```

---

## Mistake 2

Wrong loop

```csharp
for(int i = 0; i <= number; i++)
```

At

```text
i = 0
```

factorial becomes

```text
fact = 1 × 0 = 0
```

Wrong.

Always start

```csharp
i = 1;
```

---

## Mistake 3

Using `int` for large numbers.

Example

```text
13!
```

cannot fit inside an `int`.

Use

```csharp
long
```

or

```csharp
BigInteger
```

for larger factorials.

---

# Interview Questions

### Q1. Why is 0! equal to 1?

Because it is mathematically defined and is required for many combinatorial formulas.

---

### Q2. Which is better?

Iterative or Recursive?

For interviews:

✅ Iterative

because it uses

```text
O(1)
```

space.

---

### Q3. Why initialize `fact = 1`?

Because 1 is the multiplicative identity.

Anything multiplied by 1 remains unchanged.

---

### Q4. Can factorial be calculated for negative numbers?

No.

Factorial is defined only for non-negative integers.

---

# Interview Answer

> To calculate the factorial of a number, I initialize a variable `fact` to 1 and multiply it by every integer from 1 to the given number. If the input is 0, the result is 1 by definition. The iterative solution processes each number exactly once, giving a time complexity of **O(n)** and a space complexity of **O(1)**.

---

# Key Points to Remember

- Factorial means multiplication from **1 to n**.
- **0! = 1**
- Initialize **fact = 1**
- Loop from **1 to n**
- Iterative Solution → **O(n)** Time, **O(1)** Space
- Recursive Solution → **O(n)** Time, **O(n)** Space
- For large numbers, use **BigInteger**.
- Preferred interview solution: **Iterative Approach**