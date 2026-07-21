# StringBuilder Program - String Compression

## Program

```csharp
using System;
using System.Text;

class Program
{
    static void Main()
    {
        Console.WriteLine("Start small. Ship something.");

        string str = "aaabbbbbccd";
        StringBuilder result = new StringBuilder();

        int count = 1;

        for (int i = 1; i < str.Length; i++)
        {
            if (str[i] == str[i - 1])
            {
                count++;
            }
            else
            {
                result.Append(str[i - 1]);
                result.Append(count);
                count = 1;
            }
        }

        result.Append(str[^1]);
        result.Append(count);

        Console.WriteLine(result.ToString());
    }
}
```

---

# Output

```text
Start small. Ship something.
a3b5c2d1
```

---

# Program Explanation

## Step 1

```csharp
string str = "aaabbbbbccd";
```

The input string is

```text
a a a b b b b b c c d
```

We want to compress it into

```text
a3b5c2d1
```

---

## Step 2

```csharp
StringBuilder result = new StringBuilder();
```

Creates an empty `StringBuilder` object to store the compressed string.

Initially

```text
result = ""
```

---

## Step 3

```csharp
int count = 1;
```

Assume the first character has already been seen once.

```text
Current Character = a

Count = 1
```

---

## Step 4

```csharp
for (int i = 1; i < str.Length; i++)
```

The loop starts from index **1** because each character is compared with its previous character.

```csharp
str[i]
```

is compared with

```csharp
str[i - 1]
```

---

## Step 5

```csharp
if (str[i] == str[i - 1])
{
    count++;
}
```

If the current and previous characters are the same, increase the count.

Example

```text
a a a

↓

Count

1 → 2 → 3
```

Nothing is added to the result yet.

---

## Step 6

```csharp
else
{
    result.Append(str[i - 1]);
    result.Append(count);
    count = 1;
}
```

When a different character is found, it means the previous character's sequence has ended.

Example

```text
a a a b
      ↑

Different character found
```

So append

```text
a3
```

to the result.

Then reset

```text
count = 1
```

to start counting the new character (`b`).

---

## Step 7

```csharp
result.Append(str[^1]);
result.Append(count);
```

This is the most important part.

The loop only appends a character **when it finds a different next character**.

For the last character (`d`), there is **no next character**, so the `else` block never executes.

Therefore, we manually append:

```text
Last Character = d
Count = 1
```

Result becomes

```text
a3b5c2d1
```

> `str[^1]` means **the last character of the string**.

It is equivalent to

```csharp
str[str.Length - 1]
```

---

## Step 8

```csharp
Console.WriteLine(result.ToString());
```

`result` is a `StringBuilder`.

`ToString()` converts it into a normal string before printing.

Output

```text
a3b5c2d1
```

---

# Dry Run

| i | Current | Previous | Count | Result |
|---|---------|----------|------:|--------|
| Start | a | - | 1 | "" |
| 1 | a | a | 2 | "" |
| 2 | a | a | 3 | "" |
| 3 | b | a | 1 | a3 |
| 4 | b | b | 2 | a3 |
| 5 | b | b | 3 | a3 |
| 6 | b | b | 4 | a3 |
| 7 | b | b | 5 | a3 |
| 8 | c | b | 1 | a3b5 |
| 9 | c | c | 2 | a3b5 |
| 10 | d | c | 1 | a3b5c2 |
| End | d | - | 1 | a3b5c2d1 |

---

# Why Use StringBuilder Here?

Instead of writing

```csharp
result = result + str[i - 1] + count;
```

we use

```csharp
result.Append(str[i - 1]);
result.Append(count);
```

because `StringBuilder` modifies the same object instead of creating a new string every time, making the program faster and more memory-efficient.