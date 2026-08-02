# C# Class vs Struct vs Record

## Overview

In C#, there are three common ways to define custom types:

- **Class**
- **Struct**
- **Record**

Although they look similar, they differ significantly in memory allocation, equality, copying behavior, inheritance, and performance.

---

# Quick Comparison

| Feature | Class | Struct | Record |
|---------|--------|---------|---------|
| Type | Reference Type | Value Type | Reference Type (Default) |
| Stored In | Heap | Stack (Usually) / Inline | Heap |
| Can be Null | Yes | No (unless nullable) | Yes |
| Default Equality | Reference Equality | Value Equality | Value Equality |
| Supports Inheritance | ✅ Yes | ❌ No | ✅ Yes |
| Mutable | Yes | Yes | Usually Immutable |
| Garbage Collection | Yes | Minimal | Yes |
| Copy Behavior | Copies Reference | Copies Entire Value | Copies Reference |
| Best Use Case | Business Objects | Small Value Objects | DTOs, Immutable Models |

---

# 1. Class

A **class** is a **reference type**.

The object is created on the heap, and variables store a reference to that object.

## Example

```csharp
public class Employee
{
    public string Name { get; set; }
}

Employee emp1 = new Employee();
Employee emp2 = emp1;

emp2.Name = "John";

Console.WriteLine(emp1.Name);
```

### Output

```
John
```

### Why?

Both variables point to the same object.

```
Stack

emp1 ----\
          \
           -----> Heap
                  Employee
                  Name = John
          /
emp2 ----/
```

---

# Equality

```csharp
Employee e1 = new Employee();
Employee e2 = new Employee();

Console.WriteLine(e1 == e2);
```

Output

```
False
```

Classes compare **references**, not values.

---

# Advantages

- Supports inheritance
- Supports polymorphism
- Suitable for large objects
- Shared references
- Best for business models

---

# Disadvantages

- Heap allocation
- Garbage Collection overhead

---

# 2. Struct

A **struct** is a **value type**.

The variable contains the actual data.

## Example

```csharp
public struct Point
{
    public int X;
    public int Y;
}

Point p1 = new Point();
p1.X = 10;

Point p2 = p1;

p2.X = 50;

Console.WriteLine(p1.X);
```

### Output

```
10
```

Why?

Because assigning a struct copies the entire value.

```
Stack

p1
X = 10

p2
X = 50
```

Independent copies.

---

# Equality

```csharp
Point p1 = new Point { X = 10 };
Point p2 = new Point { X = 10 };

Console.WriteLine(p1.Equals(p2));
```

Output

```
True
```

Structs compare values.

---

# Advantages

- Faster allocation
- Less GC pressure
- Good for small objects
- Value semantics

---

# Disadvantages

- Copies the entire object
- No inheritance
- Large structs reduce performance

---

# Microsoft Recommendation

Use a struct only if:

- Size is less than 16 bytes
- Immutable
- Represents one value
- Rarely boxed

---

# 3. Record

A **record** is a special **reference type** introduced in C# 9.

Unlike classes, records compare values instead of references.

## Example

```csharp
public record Employee(string Name, int Age);

Employee e1 = new("John",30);
Employee e2 = new("John",30);

Console.WriteLine(e1 == e2);
```

Output

```
True
```

---

# Immutability

```csharp
public record Employee(string Name,int Age);

Employee emp = new("John",30);

// emp.Name = "David"; ❌
```

Properties are **init-only** by default.

---

# Copy using with

```csharp
Employee emp1 = new("John",30);

Employee emp2 = emp1 with
{
    Age = 31
};
```

Result

```
Employee(Name=John,Age=30)

Employee(Name=John,Age=31)
```

---

# Equality Comparison

## Class

```csharp
Employee e1 = new Employee { Name="John" };
Employee e2 = new Employee { Name="John" };

Console.WriteLine(e1 == e2);
```

Output

```
False
```

Reference equality.

---

## Struct

```csharp
Point p1 = new Point { X=10 };
Point p2 = new Point { X=10 };

Console.WriteLine(p1.Equals(p2));
```

Output

```
True
```

Value equality.

---

## Record

```csharp
Employee e1 = new("John",30);
Employee e2 = new("John",30);

Console.WriteLine(e1 == e2);
```

Output

```
True
```

Value equality.

---

# Performance Comparison

| Feature | Class | Struct | Record |
|----------|--------|---------|---------|
| Heap Allocation | Yes | No (Usually) | Yes |
| GC Pressure | High | Low | High |
| Copy Cost | Low (Reference) | High (Entire Value) | Low (Reference) |
| Equality | Reference | Value | Value |
| Inheritance | Yes | No | Yes |

---

# When Should You Use Each?

## Use Class

✔ Business models

✔ Entity Framework entities

✔ Services

✔ Objects with inheritance

Examples

- Employee
- Customer
- Product
- Order

---

## Use Struct

✔ Coordinates

✔ Color

✔ Money

✔ Point

✔ Date-like values

---

## Use Record

✔ DTOs

✔ API Request/Response

✔ CQRS Commands

✔ Events

✔ Immutable Models

---

# Common Interview Questions

## Why is Struct Faster?

Because it avoids heap allocation for small values and reduces garbage collection.

---

## Why Are Large Structs Not Recommended?

Every assignment copies the entire struct, which can become expensive.

---

## Why Use Records?

- Value equality
- Immutability
- Cleaner syntax
- Built-in cloning with `with`

---

## Can Struct Inherit Another Struct?

No.

Structs cannot inherit from classes or other structs.

They can implement interfaces.

---

## Can Records Be Mutable?

Yes.

```csharp
public record Employee
{
    public string Name { get; set; }
}
```

However, immutable records are recommended.

---

# Summary

| Scenario | Recommended Type |
|----------|-------------------|
| Business Entity | Class |
| Entity Framework Model | Class |
| Service | Class |
| Small Value Object | Struct |
| Point | Struct |
| DateTime | Struct |
| Money | Struct |
| API Request | Record |
| API Response | Record |
| DTO | Record |
| CQRS Command | Record |
| Event | Record |

---

# Interview One-Liner

- **Class** → Reference type, reference equality, supports inheritance, best for business objects.
- **Struct** → Value type, copied by value, efficient for small immutable data.
- **Record** → Reference type with value-based equality, ideal for immutable DTOs and API models.