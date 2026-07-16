# Swap Two Numbers (C# Interview Notes)

## Problem Statement

Given two numbers, swap their values.

### Example

```text
Input

A = 10
B = 20

Output

A = 20
B = 10
```

------------------------------------------------------------------------

# What is Swapping?

Swapping means exchanging the values of two variables.

Before

```text
A = 10

B = 20
```

After

```text
A = 20

B = 10
```

------------------------------------------------------------------------

# Approach 1 (Using Temporary Variable) ⭐⭐⭐⭐⭐ (Recommended)

This is the simplest, safest, and most commonly used approach in interviews and production code.

## Algorithm

1. Store `A` in a temporary variable.
2. Assign `B` to `A`.
3. Assign the temporary value to `B`.

------------------------------------------------------------------------

# Dry Run

Initially

```text
A = 10

B = 20
```

Step 1

```text
temp = A

temp = 10
```

Current Values

```text
A = 10

B = 20

temp = 10
```

--------------------------

Step 2

```text
A = B

A = 20
```

Current Values

```text
A = 20

B = 20

temp = 10
```

--------------------------

Step 3

```text
B = temp

B = 10
```

Final Values

```text
A = 20

B = 10
```

------------------------------------------------------------------------

# Dry Run Table

| Step | A | B | Temp |
|------|---:|---:|----:|
|Initial|10|20|-|
|temp = A|10|20|10|
|A = B|20|20|10|
|B = temp|20|10|10|

------------------------------------------------------------------------

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        int a = 10;
        int b = 20;

        int temp = a;
        a = b;
        b = temp;

        Console.WriteLine("A = " + a);
        Console.WriteLine("B = " + b);
    }
}
```

------------------------------------------------------------------------

# Time Complexity

Only three assignments are performed.

```text
Time Complexity = O(1)
```

------------------------------------------------------------------------

# Space Complexity

One extra variable is used.

```text
Space Complexity = O(1)
```

------------------------------------------------------------------------

# Advantages

- Very easy to understand.
- Easy to debug.
- No overflow.
- Works for all data types.
- Best interview answer.
- Preferred in production code.

------------------------------------------------------------------------

# Approach 2 (Without Temporary Variable - Addition & Subtraction)

This approach swaps two numbers using arithmetic.

## Algorithm

```text
A = A + B

B = A - B

A = A - B
```

------------------------------------------------------------------------

# Dry Run

Initially

```text
A = 10

B = 20
```

### Step 1

```text
A = A + B

A = 10 + 20

A = 30
```

Current Values

```text
A = 30

B = 20
```

--------------------------

### Step 2

```text
B = A - B

B = 30 - 20

B = 10
```

Current Values

```text
A = 30

B = 10
```

--------------------------

### Step 3

```text
A = A - B

A = 30 - 10

A = 20
```

Final Values

```text
A = 20

B = 10
```

------------------------------------------------------------------------

# Dry Run Table

| Step | A | B |
|------|---:|---:|
|Initial|10|20|
|A = A + B|30|20|
|B = A - B|30|10|
|A = A - B|20|10|

------------------------------------------------------------------------

# C# Program

```csharp
using System;

class Program
{
    static void Main()
    {
        int a = 10;
        int b = 20;

        a = a + b;
        b = a - b;
        a = a - b;

        Console.WriteLine("A = " + a);
        Console.WriteLine("B = " + b);
    }
}
```

------------------------------------------------------------------------

# Why Does It Work?

Initially

```text
A = 10

B = 20
```

After first statement

```text
A = A + B

A = 30
```

Second statement

```text
B = A - B

B = 30 - 20

B = 10
```

Now B has the original value of A.

Third statement

```text
A = A - B

A = 30 - 10

A = 20
```

Now A has the original value of B.

Both values are swapped.

------------------------------------------------------------------------

# Time Complexity

Only three arithmetic operations.

```text
Time Complexity = O(1)
```

------------------------------------------------------------------------

# Space Complexity

No extra variable is used.

```text
Space Complexity = O(1)
```

------------------------------------------------------------------------

# Drawback

This approach can fail because of **Integer Overflow**.

Example

```text
A = 2,000,000,000

B = 2,000,000,000
```

After

```text
A = A + B
```

The result exceeds the maximum value of an `int`.

This produces an incorrect result.

Therefore, this approach is **not recommended in production code**.

------------------------------------------------------------------------

# Comparison

| Feature | Temporary Variable | Addition/Subtraction |
|----------|-------------------|----------------------|
|Easy to Read|✅ Yes|⚠ Moderate|
|Easy to Debug|✅ Yes|⚠ Moderate|
|Overflow Risk|❌ No|✅ Yes|
|Extra Variable|✅ Yes|❌ No|
|Production Ready|✅ Yes|❌ No|
|Interview Preferred|⭐⭐⭐⭐⭐|⭐⭐⭐|

------------------------------------------------------------------------

# Interview Questions

### Q1. Which approach is better?

Using a **temporary variable** because it is simple, safe, and readable.

------------------------------------------------------------------------

### Q2. Why isn't Addition/Subtraction preferred?

Because it may cause **integer overflow** for very large values.

------------------------------------------------------------------------

### Q3. What is the time complexity?

Both approaches perform a constant number of operations.

```text
Time Complexity = O(1)
```

------------------------------------------------------------------------

### Q4. What is the space complexity?

Both approaches use constant extra memory.

```text
Space Complexity = O(1)
```

------------------------------------------------------------------------

# Interview Answer

> The preferred approach is to use a temporary variable because it is simple, readable, safe, and avoids overflow issues. Although swapping without a temporary variable using addition and subtraction saves one variable, it can fail when the sum exceeds the maximum value of an integer. Therefore, the temporary variable approach is recommended in both interviews and production code.

------------------------------------------------------------------------

# Key Points to Remember

- Swapping means exchanging two values.
- Best interview solution → **Temporary Variable**.
- Addition/Subtraction avoids an extra variable but has **overflow risk**.
- Time Complexity → **O(1)**.
- Space Complexity → **O(1)**.
- Preferred production solution → **Temporary Variable**.