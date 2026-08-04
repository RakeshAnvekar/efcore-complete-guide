# Entity Framework Core – Module 12
# Advanced Loading & Query Optimization

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 12 – Advanced Loading & Query Optimization

**Difficulty:** ⭐⭐⭐⭐⭐

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. Why Query Optimization?
2. Filtered Include
3. Projection using Select()
4. Cartesian Explosion
5. Single Query vs Split Query
6. Identity Resolution
7. Query Performance Tips
8. Real-Time Examples
9. Best Practices
10. Interview Questions
11. Summary
12. Key Takeaways

---

# Why Do We Need Query Optimization?

When working with Entity Framework Core, loading unnecessary data can make your application slow.

Problems caused by inefficient queries:

- High memory usage
- Slow SQL execution
- Large network traffic
- Duplicate data
- Long API response times

Example

```
Order

↓

OrderItems

↓

Product

↓

Reviews

↓

Images
```

If everything is loaded unnecessarily,

the SQL query becomes huge and performance decreases.

---

# 1. Filtered Include

## What is Filtered Include?

Filtered Include allows us to load **only the related data we actually need**.

Instead of loading every child record,

we can apply filters.

---

## Without Filtered Include

Suppose

Department

↓

100 Employees

```csharp
var departments = context.Departments
                         .Include(d => d.Employees)
                         .ToList();
```

Generated SQL

```sql
SELECT *
FROM Departments d
LEFT JOIN Employees e
ON d.Id = e.DepartmentId;
```

All 100 employees are loaded.

---

## With Filtered Include

Suppose we only need active employees.

```csharp
var departments = context.Departments
                         .Include(d => d.Employees
                             .Where(e => e.IsActive))
                         .ToList();
```

Generated SQL

```sql
SELECT *
FROM Departments d
LEFT JOIN Employees e
ON d.Id = e.DepartmentId
AND e.IsActive = 1;
```

Only active employees are loaded.

---

## Real-Time Example

HR Dashboard

Need

```
Only Active Employees
```

No reason to load

```
Resigned Employees

Inactive Employees
```

Filtered Include improves performance.

---

# Advantages

✔ Less memory

✔ Less data transfer

✔ Faster SQL

✔ Better API response

---

# 2. Projection using Select()

## What is Projection?

Projection means

Loading only the required columns instead of the complete entity.

---

Suppose Employee table contains

```
Id

Name

Salary

Email

Phone

DOB

Address

Photo

Documents
```

Your API needs only

```
Id

Name
```

---

## Bad Approach

```csharp
var employees =
context.Employees.ToList();
```

Loads every column.

---

## Good Approach

```csharp
var employees =
context.Employees
       .Select(e => new
       {
           e.Id,
           e.Name
       })
       .ToList();
```

Generated SQL

```sql
SELECT Id,
       Name
FROM Employees;
```

Only two columns are loaded.

---

## Real API Example

Instead of

```csharp
return context.Employees.ToList();
```

Use

```csharp
return context.Employees
              .Select(e => new EmployeeDto
              {
                  Id = e.Id,
                  Name = e.Name
              })
              .ToList();
```

This reduces

- Memory usage
- Network traffic
- Query execution time

---

# 3. Cartesian Explosion

## What is Cartesian Explosion?

Cartesian Explosion happens when multiple collection relationships are loaded using JOINs.

Example

```
Department

↓

Employees

↓

Projects
```

Suppose

```
Department

IT

Employees

10

Projects

5
```

Query

```csharp
context.Departments
       .Include(d => d.Employees)
       .Include(d => d.Projects)
       .ToList();
```

SQL joins

```
Department

×

Employees

×

Projects
```

Result

```
10 Employees

×

5 Projects

=

50 Rows
```

Although only

```
10 Employees

5 Projects
```

exist.

Many rows are duplicated.

This is called

```
Cartesian Explosion
```

---

# Why is it Bad?

Problems

- Duplicate rows
- More memory
- Slower SQL
- Larger network payload

---

# 4. Split Query

## Solution for Cartesian Explosion

Instead of one huge JOIN,

tell Entity Framework to execute multiple smaller queries.

```csharp
var departments =
context.Departments
       .Include(d => d.Employees)
       .Include(d => d.Projects)
       .AsSplitQuery()
       .ToList();
```

Now EF executes

```
Query 1

Departments

↓

Query 2

Employees

↓

Query 3

Projects
```

No duplicated rows.

---

# Single Query vs Split Query

## Single Query

```csharp
context.Departments
       .Include(d => d.Employees)
       .Include(d => d.Projects)
       .ToList();
```

Pros

✔ One database call

Cons

❌ Large JOIN

❌ Cartesian Explosion

---

## Split Query

```csharp
context.Departments
       .Include(d => d.Employees)
       .Include(d => d.Projects)
       .AsSplitQuery()
       .ToList();
```

Pros

✔ Less duplication

✔ Better memory usage

✔ Better for large relationships

Cons

❌ Multiple SQL queries

---

# Comparison

| Single Query | Split Query |
|---------------|-------------|
| One SQL Query | Multiple SQL Queries |
| Large JOIN | Small Queries |
| Can create Cartesian Explosion | Avoids Cartesian Explosion |
| Better for small object graphs | Better for large object graphs |

---

# 5. Identity Resolution

## Problem

Suppose

```
Employee

appears

5 times
```

Without Identity Resolution

Memory

```
Employee Object 1

Employee Object 2

Employee Object 3

Employee Object 4

Employee Object 5
```

Same employee.

Five objects.

---

## Solution

```csharp
context.Employees
       .AsNoTrackingWithIdentityResolution()
       .ToList();
```

Memory

```
One Employee Object

↓

Five References
```

Less memory consumption.

---

# 6. Query Performance Tips

## Use AsNoTracking()

For read-only queries

```csharp
context.Employees
       .AsNoTracking()
       .ToList();
```

---

## Use Projection

```csharp
.Select(...)
```

Load only required columns.

---

## Filter Before ToList()

Bad

```csharp
context.Employees
       .ToList()
       .Where(e => e.IsActive);
```

Everything is loaded first.

---

Good

```csharp
context.Employees
       .Where(e => e.IsActive)
       .ToList();
```

SQL filters data before returning it.

---

## Use Pagination

Bad

```csharp
context.Employees.ToList();
```

Loads all rows.

---

Good

```csharp
context.Employees
       .Skip(0)
       .Take(20)
       .ToList();
```

Only 20 rows are loaded.

---

## Create Database Indexes

Frequently filtered columns should have indexes.

Examples

```
Email

DepartmentId

Status

OrderDate
```

---

# Real-Time Example

Customer Dashboard

Need

```
Customer Name

Total Accounts
```

Don't load

```
Transactions

Loans

Statements

Cards
```

Instead

```csharp
var customers =
context.Customers
       .Select(c => new
       {
           c.Name,
           TotalAccounts = c.Accounts.Count
       })
       .ToList();
```

Very efficient.

---

# Performance Optimization Flow

```
Need Related Data?

↓

Yes

↓

Use Include()

↓

Multiple Collections?

↓

Yes

↓

Use AsSplitQuery()

↓

Need Only Few Columns?

↓

Yes

↓

Use Select()

↓

Read Only?

↓

Yes

↓

Use AsNoTracking()
```

---

# Best Practices

✔ Use Projection (Select) whenever possible.

✔ Use AsNoTracking() for read-only APIs.

✔ Use Filtered Include instead of loading unnecessary child entities.

✔ Use Split Queries for multiple collection Includes.

✔ Avoid loading huge object graphs.

✔ Use Pagination for large datasets.

✔ Create proper database indexes.

✔ Monitor generated SQL using logging.

---

# Interview Questions

## What is Filtered Include?

Filtered Include allows filtering related entities inside Include().

Example

```csharp
.Include(d => d.Employees.Where(e => e.IsActive))
```

---

## What is Projection?

Projection means selecting only required columns using Select().

---

## What is Cartesian Explosion?

Cartesian Explosion occurs when multiple JOINs multiply rows, producing a much larger result set than expected.

---

## How do we avoid Cartesian Explosion?

Using

```csharp
.AsSplitQuery()
```

---

## What is Split Query?

Split Query executes multiple SQL queries instead of one large JOIN query.

---

## What is Identity Resolution?

Identity Resolution ensures that duplicate occurrences of the same entity are represented by a single object instance in memory.

---

## When should we use AsNoTracking()?

Use AsNoTracking() for read-only operations where data will not be modified.

---

# Summary

Advanced Query Optimization techniques improve Entity Framework Core performance by reducing unnecessary data retrieval and optimizing SQL execution.

The main techniques include:

- Filtered Include
- Projection using Select()
- AsSplitQuery()
- AsNoTracking()
- Identity Resolution
- Pagination

Using these correctly results in faster APIs, lower memory usage, and better scalability.

---

# Key Takeaways

✔ Use Projection to load only required columns.

✔ Use Filtered Include to reduce child data.

✔ Use AsSplitQuery() for multiple collection Includes.

✔ Avoid Cartesian Explosion.

✔ Use AsNoTracking() for read-only APIs.

✔ Apply filtering before ToList().

✔ Use Pagination for large datasets.

✔ Create indexes on frequently searched columns.
