# Entity Framework Core – Performance Optimization
# Module – Compiled Queries

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** Compiled Queries

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐☆

---

# Table of Contents

1. What are Compiled Queries?
2. Why Do We Need Compiled Queries?
3. Internal Working
4. Normal Query vs Compiled Query
5. EF.CompileQuery()
6. EF.CompileAsyncQuery()
7. Real-Time Examples
8. Performance Benefits
9. When to Use Compiled Queries
10. When NOT to Use Compiled Queries
11. Best Practices
12. Interview Questions
13. Summary
14. Key Takeaways

---

# What are Compiled Queries?

A **Compiled Query** is a LINQ query that is **compiled once** and reused multiple times.

Instead of parsing and compiling the same LINQ expression every time it executes,

Entity Framework Core compiles it once and stores it as a delegate.

Every future execution reuses the compiled delegate.

---

# Why Do We Need Compiled Queries?

Suppose your API executes the following query thousands of times every minute.

```csharp
var employee = context.Employees
                      .FirstOrDefault(e => e.Id == id);
```

Every request requires EF Core to

- Analyze the LINQ expression
- Translate it
- Prepare it for SQL execution
- Execute SQL

Although EF Core already caches parts of this process internally, there is still some processing overhead.

Compiled Queries remove most of that repeated work.

---

# Internal Working

Normal Query

```
LINQ Query

↓

Expression Tree

↓

Query Translation

↓

SQL Generation

↓

Execute SQL

↓

Materialize Objects

↓

Return Result
```

This process occurs every time the query is executed.

---

# Compiled Query

```
Application Starts

↓

Compile LINQ Query Once

↓

Store Delegate

↓

Request 1

↓

Execute SQL

↓

Request 2

↓

Execute SQL

↓

Request 3

↓

Execute SQL
```

The query is compiled only once.

---

# Normal Query Example

```csharp
var employee = context.Employees
                      .FirstOrDefault(e => e.Id == id);
```

Every execution

```
Compile Query

↓

Execute SQL
```

---

# Compiled Query Example

```csharp
private static readonly Func<AppDbContext, int, Employee?> GetEmployeeById =
    EF.CompileQuery(
        (AppDbContext context, int id) =>
            context.Employees
                   .FirstOrDefault(e => e.Id == id));
```

Usage

```csharp
var employee = GetEmployeeById(context, id);
```

Now EF Core skips the query compilation step.

---

# Internal Flow

Without Compiled Query

```
Request

↓

LINQ Query

↓

Compile

↓

SQL

↓

Database

↓

Result
```

---

With Compiled Query

```
Application Starts

↓

Compile Once

↓

Delegate Stored

↓

Request

↓

Execute SQL

↓

Database

↓

Result
```

---

# How EF.CompileQuery() Works

```csharp
private static readonly Func<AppDbContext, int, Employee?> GetEmployee =
    EF.CompileQuery(
        (AppDbContext context, int id) =>
            context.Employees
                   .FirstOrDefault(e => e.Id == id));
```

Explanation

```
EF.CompileQuery()

↓

Expression Tree

↓

Compiled Delegate

↓

Stored in Memory

↓

Reusable Function
```

---

# Query with Multiple Parameters

```csharp
private static readonly Func<AppDbContext, string, bool, Employee?> GetEmployee =
    EF.CompileQuery(
        (AppDbContext context,
         string department,
         bool active) =>
             context.Employees
                    .FirstOrDefault(e =>
                        e.Department == department &&
                        e.IsActive == active));
```

Usage

```csharp
var employee = GetEmployee(context, "IT", true);
```

---

# EF.CompileAsyncQuery()

For asynchronous streaming queries

```csharp
private static readonly Func<AppDbContext, IAsyncEnumerable<Employee>> GetEmployees =
    EF.CompileAsyncQuery(
        (AppDbContext context) =>
            context.Employees);
```

Usage

```csharp
await foreach(var employee in GetEmployees(context))
{
    Console.WriteLine(employee.Name);
}
```

---

# Performance Comparison

| Normal Query | Compiled Query |
|--------------|----------------|
| Compiles repeatedly | Compiled once |
| Higher CPU usage | Lower CPU usage |
| More processing | Less processing |
| Good for normal CRUD | Best for frequently executed queries |

---

# Real-Time Banking Example

Suppose a Banking API is called

```
500,000 Times Per Day
```

Query

```csharp
context.Customers
       .FirstOrDefault(c => c.AccountNumber == accountNumber);
```

Instead of recompiling the query every request,

compile it once.

```csharp
private static readonly Func<AppDbContext, string, Customer?> GetCustomer =
    EF.CompileQuery(
        (AppDbContext context, string accountNumber) =>
            context.Customers
                   .FirstOrDefault(c => c.AccountNumber == accountNumber));
```

Usage

```csharp
var customer = GetCustomer(context, accountNumber);
```

Better performance.

---

# Real-Time E-Commerce Example

Product Details API

```
Called Millions of Times
```

Normal Query

```csharp
context.Products
       .FirstOrDefault(p => p.Id == id);
```

Compiled Query

```csharp
private static readonly Func<AppDbContext, int, Product?> GetProduct =
    EF.CompileQuery(
        (AppDbContext context, int id) =>
            context.Products
                   .FirstOrDefault(p => p.Id == id));
```

Less CPU overhead.

---

# When Should We Use Compiled Queries?

Use Compiled Queries when

✔ Same query executes thousands of times

✔ High-performance APIs

✔ Banking Applications

✔ Payment Systems

✔ Inventory Systems

✔ Frequently used lookup APIs

✔ Gaming Servers

✔ Financial Applications

---

# When Should We NOT Use Compiled Queries?

Avoid Compiled Queries when

❌ Query shape changes dynamically

Example

```csharp
var query = context.Employees.AsQueryable();

if(name != null)
    query = query.Where(e => e.Name == name);

if(department != null)
    query = query.Where(e => e.Department == department);

return query.ToList();
```

Since the query changes,

Compiled Queries provide little benefit.

---

Avoid for

- Search Screens
- Admin Reports
- One-Time Queries
- Dynamic Filters

---

# Memory Diagram

Normal Query

```
Request

↓

Compile Query

↓

Execute SQL

↓

Result
```

Every request repeats compilation.

---

Compiled Query

```
Application Starts

↓

Compile Once

↓

Delegate Stored

↓

Request

↓

Execute SQL

↓

Result
```

---

# Best Practices

✔ Store compiled queries as `static readonly`.

✔ Compile only frequently executed queries.

✔ Keep the query shape fixed.

✔ Parameterize values instead of changing query structure.

✔ Measure performance before using Compiled Queries.

✔ Use normal LINQ for regular CRUD operations.

---

# Interview Questions

## What is a Compiled Query?

A Compiled Query is a LINQ query compiled once into a reusable delegate using `EF.CompileQuery()`.

---

## Why do we use Compiled Queries?

To reduce query compilation overhead and improve performance for frequently executed queries.

---

## Which method is used?

```csharp
EF.CompileQuery()
```

---

## What is the async version?

```csharp
EF.CompileAsyncQuery()
```

---

## Should every query be compiled?

No.

Only frequently executed queries should be compiled.

---

## Do Compiled Queries reduce database execution time?

No.

Compiled Queries reduce **Entity Framework's query compilation overhead**.

The SQL still executes against the database in the normal way.

---

# Summary

Compiled Queries are an advanced performance optimization in Entity Framework Core.

Instead of compiling a LINQ query every time it executes, EF Core compiles it once and stores it as a delegate.

Future executions reuse the compiled delegate, reducing CPU usage and improving performance in high-throughput applications.

Compiled Queries are best suited for APIs that repeatedly execute the same query with different parameter values.

---

# Key Takeaways

✔ Compiled Queries improve EF Core performance.

✔ Uses `EF.CompileQuery()`.

✔ Compiles LINQ once.

✔ Reuses the compiled delegate.

✔ Best for frequently executed queries.

✔ Does **not** reduce SQL execution time.

✔ Does **not** improve dynamic queries.

✔ Store compiled queries as `static readonly`.

---

# Easy Interview Trick

## Normal Query

```
Every Request

↓

Compile

↓

Execute SQL
```

---

## Compiled Query

```
Application Startup

↓

Compile Once

↓

Store Delegate

↓

Every Request

↓

Execute SQL
```

---

# Next Module
