# Entity Framework Core – Module 9
# Lazy Loading

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 9 – Lazy Loading

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Lazy Loading?
2. Why Do We Need Lazy Loading?
3. Real-Time Example
4. How Lazy Loading Works
5. Internal Working
6. Proxy Objects
7. Memory Diagram
8. Enabling Lazy Loading
9. Lazy Loading Example
10. N+1 Query Problem
11. Performance
12. Advantages
13. Disadvantages
14. Lazy vs Eager Loading
15. Best Practices
16. Interview Questions
17. Summary
18. Key Takeaways

---

# What is Lazy Loading?

Lazy Loading is a feature in Entity Framework Core where **related entities are loaded only when they are actually accessed**.

Entity Framework **does not load navigation properties immediately**.

Instead, it waits until the navigation property is used.

---

# Why Do We Need Lazy Loading?

Imagine an application where users only need department information.

Database

```
Department

---------------------

Id

Name

1

IT
```

Employee Table

```
Id

Name

DepartmentId

1

Rakesh

1

2

Raj

1

3

Amit

1
```

If users only need

```
Department Name
```

there is no need to load employees.

Lazy Loading loads employees only if they are actually requested.

---

# Real-Time Example

Suppose we have two entities.

```
Department

↓

Employees
```

Entity Classes

```csharp
public class Department
{
    public int Id { get; set; }

    public string Name { get; set; }

    public virtual ICollection<Employee> Employees { get; set; }
}

public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }

    public int DepartmentId { get; set; }

    public virtual Department Department { get; set; }
}
```

Notice

```
virtual
```

is very important.

---

# First Query

```csharp
var department =
context.Departments.First();
```

Generated SQL

```sql
SELECT *

FROM Departments

LIMIT 1;
```

Question

Did Employees load?

Answer

```
No
```

Only Department is loaded.

---

# Access Navigation Property

Later

```csharp
var employees =
department.Employees;
```

Now Entity Framework executes another SQL query.

```sql
SELECT *

FROM Employees

WHERE DepartmentId = 1;
```

Employees are loaded only now.

This is Lazy Loading.

---

# Why is it Called Lazy?

Imagine ordering food at a restaurant.

You first order

```
Main Course
```

The waiter does not bring dessert.

Later you ask

```
Dessert Please
```

Only then the dessert arrives.

Entity Framework behaves exactly the same.

It loads related data only when requested.

---

# Internal Working

```
Application

↓

Department Loaded

↓

Proxy Object Created

↓

Navigation Property Accessed

↓

Proxy Detects Access

↓

Generate SQL

↓

Load Related Data
```

---

# Proxy Objects

Entity Framework does not directly create

```
Department
```

Instead,

it creates

```
DepartmentProxy
```

which inherits from

```
Department
```

Example

```
Department

↑

DepartmentProxy
```

The proxy overrides navigation properties.

Whenever

```csharp
department.Employees
```

is accessed,

the proxy automatically executes SQL.

---

# Memory Diagram

Initially

```
STACK

department

↓

DepartmentProxy

-----------------------

HEAP

Department

Id = 1

Name = IT

Employees = NULL
```

After

```csharp
department.Employees
```

```
STACK

department

↓

DepartmentProxy

-----------------------

HEAP

Department

Id = 1

Name = IT

Employees

↓

Loaded
```

---

# How to Enable Lazy Loading

## Step 1

Install NuGet Package

```
Microsoft.EntityFrameworkCore.Proxies
```

---

## Step 2

Enable Proxy Support

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
{
    options.UseLazyLoadingProxies()
           .UseSqlServer(connectionString);
});
```

---

## Step 3

Navigation Properties must be

```
virtual
```

Example

```csharp
public virtual ICollection<Employee> Employees
{
    get;
    set;
}
```

Without

```
virtual
```

Lazy Loading will not work.

---

# Complete Example

```csharp
using var context = new AppDbContext();

var department =
context.Departments.First();

Console.WriteLine(department.Name);
```

SQL Generated

```sql
SELECT *

FROM Departments
```

No Employee query.

Now

```csharp
Console.WriteLine(
department.Employees.Count);
```

Entity Framework executes

```sql
SELECT *

FROM Employees

WHERE DepartmentId = 1;
```

Automatically.

---

# N+1 Query Problem

Suppose

```
100 Departments
```

Query

```csharp
var departments =
context.Departments.ToList();
```

SQL

```sql
SELECT *

FROM Departments;
```

Only one query.

Now

```csharp
foreach(var department in departments)
{
    Console.WriteLine(
    department.Employees.Count);
}
```

Entity Framework executes

```
Department 1

↓

Employee Query

Department 2

↓

Employee Query

Department 3

↓

Employee Query

...

Department 100

↓

Employee Query
```

Total Queries

```
1

+

100

=

101 Queries
```

This is called

```
N+1 Query Problem
```

---

# Why is N+1 a Problem?

Suppose each query takes

```
20 ms
```

Total

```
101 × 20

=

2020 ms
```

More than 2 seconds.

If we had loaded everything in one query,

it could complete much faster.

---

# Performance Comparison

| Feature | Lazy Loading |
|----------|--------------|
| Initial Query | Small |
| Related Data | Loaded Later |
| Number of Queries | Multiple |
| Memory Usage | Low Initially |
| Performance | Can Decrease Due to Multiple Queries |

---

# Advantages

✔ Loads data only when needed.

✔ Small initial SQL query.

✔ Lower initial memory usage.

✔ Easy to write.

✔ Good for small applications.

---

# Disadvantages

❌ Generates multiple SQL queries.

❌ Can cause N+1 Query Problem.

❌ Hard to predict generated SQL.

❌ Not recommended for large APIs.

---

# Lazy Loading vs Eager Loading

| Lazy Loading | Eager Loading |
|--------------|---------------|
| Related data loaded when accessed | Related data loaded immediately |
| Multiple SQL queries | Usually one SQL query |
| Can cause N+1 Problem | Avoids N+1 Problem |
| Simpler code | Better performance for known related data |

---

# When Should We Use Lazy Loading?

Good for

- Desktop Applications
- WinForms
- WPF
- Small CRUD Applications
- Internal Tools

Avoid for

- ASP.NET Core APIs
- Microservices
- Reporting
- Dashboard
- High-performance Applications

---

# Best Practices

✔ Use Lazy Loading only when related data is rarely needed.

✔ Avoid Lazy Loading inside loops.

✔ Prefer Eager Loading for APIs.

✔ Always monitor generated SQL.

✔ Be aware of the N+1 Query Problem.

---

# Interview Questions

## What is Lazy Loading?

Lazy Loading is a technique where Entity Framework loads related entities only when the navigation property is accessed.

---

## Why do we use virtual?

Because Entity Framework creates a proxy class that overrides virtual navigation properties and loads related data automatically.

---

## Is Lazy Loading enabled by default?

No.

It must be enabled by

- Installing

```
Microsoft.EntityFrameworkCore.Proxies
```

- Calling

```csharp
UseLazyLoadingProxies()
```

- Declaring navigation properties as

```
virtual
```

---

## What is the N+1 Query Problem?

One query loads parent entities.

Then one additional query is executed for each parent to load related data.

Example

```
1 Department Query

+

100 Employee Queries

=

101 Queries
```

---

## Does Lazy Loading improve performance?

It depends.

For small applications,

it may reduce the initial query size.

For large applications,

it often reduces performance because of additional SQL queries.

---

# Summary

Lazy Loading loads related entities only when they are actually accessed through navigation properties.

It works by creating proxy objects that intercept navigation property access and automatically execute additional SQL queries.

Although Lazy Loading can simplify development and reduce the amount of data retrieved initially, it may lead to the N+1 Query Problem and should be used carefully in production applications.

---

# Key Takeaways

✔ Lazy Loading delays loading of related entities.

✔ Navigation properties must be virtual.

✔ Requires Microsoft.EntityFrameworkCore.Proxies.

✔ Use UseLazyLoadingProxies() to enable it.

✔ Related data loads only when accessed.

✔ Can cause the N+1 Query Problem.

✔ Use cautiously in ASP.NET Core applications.

✔ Prefer Eager Loading for APIs and Microservices.

---

# Next Module

## Module 10 – Eager Loading

Topics Covered

- Include()
- ThenInclude()
- Multiple Include()
- Split Queries
- Single Query vs Split Query
- Performance
- Internal Working
- Memory Diagram
- Best Practices
- Interview Questions