# Module 3.1 – Stack vs Heap Memory

> **Course:** C# Complete Interview Master Roadmap

**Module:** Stack vs Heap

**Difficulty:** ⭐⭐⭐☆☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Memory?
2. What is Stack Memory?
3. What is Heap Memory?
4. Stack vs Heap Architecture
5. Value Types
6. Reference Types
7. Stack Allocation Example
8. Heap Allocation Example
9. Real-Time Example
10. Stack vs Heap Comparison
11. Advantages
12. Interview Questions
13. Summary
14. Key Takeaways

---

# What is Memory?

Whenever a C# program runs,

the CLR allocates memory to store variables and objects.

The CLR mainly uses two memory areas:

```
1. Stack Memory

2. Heap Memory
```

---

# Memory Architecture

```
                    CLR Memory

                       │

         ┌─────────────┴─────────────┐

         ▼                           ▼

      Stack                       Heap
```

---

# What is Stack Memory?

Stack is a memory area used to store

- Local Variables
- Method Parameters
- Value Types
- References (addresses) to Heap objects

Stack follows the

```
LIFO

Last In

First Out
```

principle.

---

# Stack Example

Imagine a stack of plates.

```
       Plate 3

       Plate 2

       Plate 1
```

You always remove the top plate first.

Exactly the same happens in Stack Memory.

---

# Characteristics of Stack

✔ Very Fast

✔ Automatically Managed

✔ Stores Local Variables

✔ Stores Value Types

✔ Memory is automatically released when a method finishes.

---

# What is Heap Memory?

Heap is a memory area used to store

```
Objects

Arrays

Strings

Collections

Reference Types
```

Heap memory is managed by the

```
Garbage Collector (GC)
```

---

# Characteristics of Heap

✔ Large Memory

✔ Stores Objects

✔ Slower than Stack

✔ Managed by Garbage Collector

✔ Objects remain alive until no references exist

---

# Stack vs Heap Architecture

Suppose

```csharp
int age = 25;

Person person = new Person();
```

Memory

```
STACK

age = 25

person ----┐
            │
            ▼

HEAP

Person Object
```

Notice

The actual object lives in the Heap.

The reference variable lives in the Stack.

---

# Example 1 – Value Type

```csharp
int number = 100;
```

Memory

```
STACK

number = 100
```

Nothing is stored in Heap.

---

# Example 2 – Reference Type

```csharp
class Employee
{
    public string Name;
}

Employee emp = new Employee();

emp.Name = "Rakesh";
```

Memory

```
STACK

emp ----------┐
              │
              ▼

HEAP

Employee Object

Name = "Rakesh"
```

---

# Example 3 – Multiple References

```csharp
Employee emp1 = new Employee();

Employee emp2 = emp1;
```

Memory

```
STACK

emp1 --------┐

emp2 --------┘

              │
              ▼

HEAP

Employee Object
```

Both variables point to the same object.

---

# Real-Time Example

Imagine a company.

Employees have ID cards.

```
ID Card

↓

Employee
```

The ID card is like the **reference** (Stack).

The employee is like the **object** (Heap).

Two people can hold copies of the same employee record (reference), but there is only one employee object.

---

# Method Call Example

```csharp
static void Main()
{
    Display();
}

static void Display()
{
    int x = 10;
}
```

Execution

```
Main()

↓

Display()

↓

Display Ends

↓

Main Ends
```

Stack Memory

```
------------------

Display()

x = 10

------------------

Main()

------------------
```

When `Display()` finishes,

its stack frame is removed automatically.

---

# Heap Example

```csharp
Employee emp = new Employee();
```

Memory

```
STACK

emp -----------┐
               │
               ▼

HEAP

Employee Object
```

When

```csharp
emp = null;
```

Memory

```
STACK

emp = null

HEAP

Employee Object

↓

Eligible for Garbage Collection
```

The object is removed later by the Garbage Collector.

---

# Stack vs Heap Comparison

| Stack | Heap |
|--------|------|
| Stores Value Types | Stores Reference Type Objects |
| Fast | Slower |
| Small Memory | Large Memory |
| Automatically Cleared | Cleared by Garbage Collector |
| LIFO Structure | No LIFO |
| Thread Specific | Shared Across Threads |
| No Fragmentation | Can Fragment Over Time |
| Stores References | Stores Actual Objects |

---

# Advantages of Stack

✔ Faster Memory Allocation

✔ Automatic Cleanup

✔ Better Performance

✔ Thread Safe

---

# Advantages of Heap

✔ Can Store Large Objects

✔ Dynamic Memory Allocation

✔ Shared Between Methods

✔ Supports Object-Oriented Programming

---

# Interview Questions

## What is Stack?

Stack is a memory area used to store local variables, method parameters, value types, and references to objects.

---

## What is Heap?

Heap is a memory area used to store objects and reference types.

---

## Which is faster?

```
Stack
```

because allocation and deallocation are very simple.

---

## Which is larger?

```
Heap
```

---

## Who manages Stack?

The CLR automatically manages Stack memory.

---

## Who manages Heap?

The Garbage Collector manages Heap memory.

---

## Where are objects stored?

```
Heap
```

---

## Where are references stored?

```
Stack
```

---

## What happens when a method finishes?

Its stack frame is automatically removed.

---

## When is Heap memory released?

When no references point to the object, it becomes eligible for Garbage Collection.

---

# Summary

C# uses two primary memory areas:

- **Stack** for local variables, value types, and object references.
- **Heap** for storing actual objects and reference types.

Stack memory is fast and automatically cleaned up when methods complete.

Heap memory stores dynamically allocated objects and is managed by the Garbage Collector.

Understanding Stack and Heap is essential for learning Value Types, Reference Types, Garbage Collection, and performance optimization.

---

# Key Takeaways

✔ Stack stores local variables and references.

✔ Heap stores actual objects.

✔ Stack is faster than Heap.

✔ Stack uses LIFO.

✔ Heap is managed by the Garbage Collector.

✔ Value Types are usually stored in Stack.

✔ Reference Type objects are stored in Heap.

✔ References to objects are stored in Stack.

---

# Next Module

## Module 3.2 – Value Types vs Reference Types

Topics Covered

- Value Types
- Reference Types
- Assignment Behavior
- Copy Semantics
- Memory Diagrams
- Interview Questions
- Performance Considerations