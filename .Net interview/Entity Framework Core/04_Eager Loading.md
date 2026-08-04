# Entity Framework Core – Module 10
# Eager Loading

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 10 – Eager Loading

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Eager Loading?
2. Why Do We Need Eager Loading?
3. Real-Time Example
4. How Eager Loading Works
5. Internal Working
6. Memory Diagram
7. Include()
8. ThenInclude()
9. Multiple Include()
10. Performance
11. Advantages
12. Disadvantages
13. Lazy Loading vs Eager Loading
14. Best Practices
15. Interview Questions
16. Summary
17. Key Takeaways

---

# What is Eager Loading?

Eager Loading is a technique where Entity Framework loads the main entity and its related entities **immediately**.

Unlike Lazy Loading,

related data is loaded **before** we access it.

---

# Why Do We Need Eager Loading?

Suppose we have

```
Department

↓

Employees
```

Department Table

| Id | Name |
|----|------|
|1|IT|
|2|HR|
|3|Finance|

Employee Table

|Id|Name|DepartmentId|
|--|----|------------|
|1|Rakesh|1|
|2|Raj|1|
|3|Amit|2|
|4|Kiran|3|

Suppose our application needs

- Department Name
- Employee List

Instead of loading Employees later,

we load everything together.

---

# Real-Time Example

Without Eager Loading

```csharp
var departments =
context.Departments.ToList();
```

SQL

```sql
SELECT *
FROM Departments;
```

Employees are **not loaded**.

Later

```csharp
foreach(var department in departments)
{
    Console.WriteLine(department.Employees.Count);
}
```

Entity Framework executes

```sql
SELECT *
FROM Employees
WHERE DepartmentId = 1;

SELECT *
FROM Employees
WHERE DepartmentId = 2;

SELECT *
FROM Employees
WHERE DepartmentId = 3;
```

Multiple SQL queries.

---

# Eager Loading

Now write

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .ToList();
```

Entity Framework understands

```
Developer also needs Employees.
```

It loads

- Departments
- Employees

together.

---

# SQL Generated

Conceptually

```sql
SELECT
    d.Id,
    d.Name,
    e.Id,
    e.Name
FROM Departments d
LEFT JOIN Employees e
ON d.Id = e.DepartmentId;
```

Everything is loaded in one operation.

---

# Internal Working

```
Application

↓

Include()

↓

Generate SQL

↓

Load Departments

↓

Load Employees

↓

Create Objects

↓

Store in Change Tracker

↓

Return Data
```

---

# Memory Diagram

After executing

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .ToList();
```

Memory

```
STACK

departments

↓

Reference

----------------------------

HEAP

Department

↓

Employees

↓

Already Loaded
```

No additional SQL is required later.

---

# Access Navigation Property

```csharp
foreach(var department in departments)
{
    Console.WriteLine(department.Name);

    Console.WriteLine(
        department.Employees.Count);
}
```

Question

Will Entity Framework execute SQL again?

Answer

```
No
```

Employees are already available in memory.

---

# Include()

Use Include() to load one related entity.

Example

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .ToList();
```

Department

↓

Employees

Both are loaded.

---

# Multiple Include()

Suppose

```
Department

↓

Employees

↓

Projects
```

Code

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .Include(d => d.Projects)
       .ToList();
```

Entity Framework loads

- Employees
- Projects

together.

---

# ThenInclude()

Suppose

```
Department

↓

Employees

↓

Address
```

Entities

```csharp
public class Department
{
    public ICollection<Employee> Employees { get; set; }
}

public class Employee
{
    public Address Address { get; set; }
}
```

Now write

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .ThenInclude(e => e.Address)
       .ToList();
```

Everything is loaded

```
Department

↓

Employees

↓

Address
```

---

# Real-Time Banking Example

Suppose

```
Customer

↓

Accounts

↓

Transactions
```

Instead of

```
Customer Query

↓

Account Query

↓

Transaction Query
```

We write

```csharp
var customers =
context.Customers
       .Include(c => c.Accounts)
       .ThenInclude(a => a.Transactions)
       .ToList();
```

Everything loads together.

---

# Why Eager Loading Solves N+1

Lazy Loading

```
Departments

↓

1 Query

↓

Employees

↓

100 Queries

↓

101 Queries
```

Eager Loading

```
Departments

+

Employees

↓

Single Query

↓

Done
```

Only one operation.

---

# Performance Comparison

| Feature | Eager Loading |
|----------|---------------|
| Number of Queries | Usually One (or configured split queries) |
| Related Data | Loaded Immediately |
| N+1 Problem | Avoided |
| Memory Usage | Higher Initially |
| Performance | Better for APIs |

---

# Advantages

✔ Avoids N+1 Query Problem

✔ Better Performance

✔ Predictable SQL

✔ Fewer Database Round Trips

✔ Best for APIs

✔ Best for Reporting

---

# Disadvantages

❌ Can load unnecessary data

❌ Large JOIN queries may become complex

❌ More memory used initially

---

# Lazy Loading vs Eager Loading

| Lazy Loading | Eager Loading |
|--------------|---------------|
| Related data loaded when accessed | Related data loaded immediately |
| Uses virtual navigation properties | Uses Include() |
| Multiple SQL queries | Usually one SQL query |
| Can cause N+1 Problem | Avoids N+1 Problem |
| Good for small applications | Best for Web APIs |

---

# Best Practices

✔ Use Include() when related data is definitely required.

✔ Avoid unnecessary Include() statements.

✔ Use ThenInclude() for nested relationships.

✔ Prefer Eager Loading for APIs.

✔ Monitor generated SQL for complex object graphs.

---

# Interview Questions

## What is Eager Loading?

Eager Loading loads the main entity and its related entities immediately using Include().

---

## Which method is used?

```csharp
Include()

ThenInclude()
```

---

## Does Eager Loading solve the N+1 Problem?

Yes.

Because related entities are loaded together instead of executing one query per parent.

---

## What is Include()?

Include() loads a related entity.

Example

```csharp
context.Departments
       .Include(d => d.Employees);
```

---

## What is ThenInclude()?

ThenInclude() loads nested related entities.

Example

```csharp
context.Departments
       .Include(d => d.Employees)
       .ThenInclude(e => e.Address);
```

---

## When should we use Eager Loading?

Use Eager Loading when

- Building REST APIs
- Reports
- Dashboard
- Export Features
- Microservices
- Whenever related data is definitely required

---

# Summary

Eager Loading retrieves the main entity and its related entities together using `Include()` and `ThenInclude()`.

It reduces the number of database round trips, avoids the N+1 Query Problem, and is generally the preferred loading strategy for ASP.NET Core Web APIs and production applications where related data is known in advance.

---

# Key Takeaways

✔ Eager Loading loads related entities immediately.

✔ Uses Include() and ThenInclude().

✔ Avoids the N+1 Query Problem.

✔ Produces fewer database queries.

✔ Best suited for Web APIs and Microservices.

✔ Can retrieve more data than necessary if overused.

✔ Prefer Eager Loading when you know related data is needed.

---

