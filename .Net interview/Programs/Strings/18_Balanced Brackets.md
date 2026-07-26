# Balanced Brackets (C# Interview Notes)

> **Interview Importance:** ⭐⭐⭐⭐⭐

Balanced Brackets is one of the most frequently asked Stack-based interview questions. It checks whether every opening bracket has a corresponding closing bracket in the correct order.

---

# Problem Statement

Given a string containing brackets:

- `()`
- `{}`
- `[]`

Determine whether the brackets are balanced.

A string is balanced if:

- Every opening bracket has a matching closing bracket.
- Brackets close in the correct order.
- No extra opening or closing brackets remain.

---

# Examples

## Example 1

```text
Input

()

Output

True
```

---

## Example 2

```text
Input

({[]})

Output

True
```

---

## Example 3

```text
Input

([)]

Output

False
```

---

## Example 4

```text
Input

((

Output

False
```

---

# Why Stack?

Stack follows the **LIFO (Last In First Out)** principle.

Example

```
Open (

Open {

Open [

Now ] comes

Which bracket should close first?

[
```

The **last opened bracket** must close first.

That's exactly how a Stack works.

---

# Algorithm

1. Create an empty Stack.
2. Traverse each character.
3. If it's an opening bracket, push it.
4. If it's a closing bracket:
   - If the stack is empty → Not Balanced.
   - Pop the top bracket.
   - Check whether it matches.
5. After processing all characters:
   - If the stack is empty → Balanced.
   - Otherwise → Not Balanced.

---

# Dry Run

Input

```text
({[]})
```

Initially

```
Stack = Empty
```

---

### Step 1

Current Character

```
(
```

Push

```
Stack

(
```

---

### Step 2

Current Character

```
{
```

Push

```
Stack

{
(
```

---

### Step 3

Current Character

```
[
```

Push

```
Stack

[
{
(
```

---

### Step 4

Current Character

```
]
```

Pop

```
Removed = [
```

Compare

```
] matches [

✔ Correct
```

Remaining Stack

```
{
(
```

---

### Step 5

Current Character

```
}
```

Pop

```
Removed = {
```

Compare

```
} matches {

✔ Correct
```

Remaining Stack

```
(
```

---

### Step 6

Current Character

```
)
```

Pop

```
Removed = (
```

Compare

```
) matches (

✔ Correct
```

Remaining Stack

```
Empty
```

Since the stack is empty,

```
Answer = True
```

---

# Dry Run Table

| Character | Action | Stack |
|------------|--------|-------|
| ( | Push | ( |
| { | Push | ( { |
| [ | Push | ( { [ |
| ] | Pop [ | ( { |
| } | Pop { | ( |
| ) | Pop ( | Empty |

---

# C# Code

```csharp
using System;
using System.Collections.Generic;

class Program
{
    static bool IsBalanced(string input)
    {
        Stack<char> stack = new Stack<char>();

        foreach (char ch in input)
        {
            // Push opening brackets
            if (ch == '(' || ch == '{' || ch == '[')
            {
                stack.Push(ch);
            }
            else
            {
                // No opening bracket available
                if (stack.Count == 0)
                    return false;

                char top = stack.Pop();

                // Check matching brackets
                if ((ch == ')' && top != '(') ||
                    (ch == '}' && top != '{') ||
                    (ch == ']' && top != '['))
                {
                    return false;
                }
            }
        }

        // Stack must be empty
        return stack.Count == 0;
    }

    static void Main()
    {
        string input = "({[]})";

        Console.WriteLine(IsBalanced(input));
    }
}
```

---

# Code Explanation (Line by Line)

---

## Step 1

```csharp
Stack<char> stack = new Stack<char>();
```

Creates an empty stack.

Initially

```
Stack

Empty
```

---

## Step 2

```csharp
foreach(char ch in input)
```

Reads one character at a time.

Example

```
(

↓

{

↓

[

↓

]

↓

}

↓

)
```

---

## Step 3

```csharp
if(ch=='(' || ch=='{' || ch=='[')
```

Checks whether the current character is an opening bracket.

Example

```
Current = {

↓

Opening Bracket

↓

Push
```

---

## Step 4

```csharp
stack.Push(ch);
```

Adds the opening bracket to the top of the stack.

Example

Before

```
(
```

After Push

```
{
(
```

---

## Step 5

```csharp
if(stack.Count==0)
    return false;
```

Why?

Suppose input is

```
)
```

There is no opening bracket available.

Trying to pop from an empty stack will throw an exception.

So return

```
False
```

---

## Step 6

```csharp
char top = stack.Pop();
```

Removes the top bracket.

Example

Before

```
[
{
(
```

After Pop

```
Removed = [

Remaining

{
(
```

---

## Step 7

```csharp
if((ch==')' && top!='(') ||
   (ch=='}' && top!='{') ||
   (ch==']' && top!='['))
{
    return false;
}
```

This checks whether the closing bracket matches the opening bracket.

Example

Current

```
]
```

Removed

```
[
```

Match ✔

Continue.

Example

Current

```
]
```

Removed

```
{
```

Mismatch ❌

Return

```
False
```

---

## Step 8

```csharp
return stack.Count==0;
```

After processing all characters,

If stack is

```
Empty
```

Return

```
True
```

Otherwise

```
False
```

Example

Input

```
(((
```

Stack

```
(
(
(
```

Not Empty

Return

```
False
```

---

# Why Do We Check `stack.Count == 0` Before Pop?

Suppose

```text
Input

)
```

Stack

```
Empty
```

If you execute

```csharp
stack.Pop();
```

Runtime Error

```
InvalidOperationException
```

So always check

```csharp
if(stack.Count==0)
    return false;
```

---

# Why Do We Check `stack.Count == 0` At The End?

Example

```
(((
```

All characters are opening brackets.

Nothing was popped.

Stack

```
(
(
(
```

Still contains elements.

Hence

```
Not Balanced
```

---

# Why Can't We Use a Counter?

Suppose

```
([)]
```

Opening Brackets = 2

Closing Brackets = 2

Counter says

```
Balanced
```

But actual order is

```
(

↓

[

↓

)

↓

]
```

This is incorrect.

A Stack remembers the order.

A Counter does not.

---

# Time Complexity

Each character is visited exactly once.

```
O(n)
```

---

# Space Complexity

Worst case

```
(((((((
```

Every opening bracket goes into the stack.

```
O(n)
```

---

# Edge Cases

### Empty String

```
Input

""

Output

True
```

---

### Only Opening Brackets

```
Input

(((

Output

False
```

---

### Only Closing Brackets

```
Input

)))

Output

False
```

---

### Wrong Order

```
Input

([)]

Output

False
```

---

### Nested Brackets

```
Input

({[]})

Output

True
```

---

# Common Mistakes

## Mistake 1

Calling

```csharp
stack.Pop();
```

without checking

```csharp
stack.Count==0
```

---

## Mistake 2

Checking only the number of brackets instead of their order.

---

## Mistake 3

Forgetting to check whether the stack is empty after the loop.

---

## Mistake 4

Comparing opening brackets with opening brackets.

Wrong

```csharp
(c=='(' && top==')')
```

Correct

```csharp
(c==')' && top!='(')
```

---

# Interview Questions

### Q1. Which data structure is used?

```
Stack
```

---

### Q2. Why Stack?

Because brackets follow the **LIFO (Last In First Out)** principle.

---

### Q3. Why can't we use a counter?

A counter checks only the number of brackets, not whether they are closed in the correct order.

---

### Q4. What is the time complexity?

```
O(n)
```

---

### Q5. What is the space complexity?

```
O(n)
```

---

# Interview Answer

> The solution uses a **Stack** to keep track of opening brackets. Every opening bracket is pushed onto the stack. When a closing bracket is encountered, the top element is popped and compared to ensure it is the correct matching opening bracket. If a mismatch occurs, or if the stack is empty when a closing bracket is found, the string is not balanced. After processing all characters, the stack must be empty for the brackets to be considered balanced. This solution runs in **O(n)** time with **O(n)** auxiliary space.

---

# Key Points to Remember

- Use **Stack** (LIFO).
- Push opening brackets.
- Pop for closing brackets.
- Always check if the stack is empty before `Pop()`.
- Verify matching pairs.
- The stack must be empty at the end.
- **Time Complexity:** O(n)
- **Space Complexity:** O(n)