# IEnumerable vs IQueryable in C#

## Overview

`IEnumerable` and `IQueryable` are two of the most commonly asked interview topics in C# and Entity Framework.

Although they appear similar, they work very differently.

The biggest difference is **where the query is executed**.

- **IEnumerable** → Query executes in **Application Memory (Client Side)**
- **IQueryable** → Query executes in **Database (Server Side)**

---

# Quick Comparison

| Feature | IEnumerable | IQueryable |
|----------|-------------|------------|
| Namespace | System.Collections | System.Linq |
| Query Execution | Client Side | Server Side |
| Data Source | In-Memory Collections | Database, Remote Source |
| Filtering | In Memory | In Database |
| Deferred Execution | ✅ Yes | ✅ Yes |
| Supports LINQ | LINQ to Objects | LINQ to Entities |
| Performance | Slower for large data | Faster for large data |
| SQL Generation | ❌ No | ✅ Yes |
| Best Use Case | Collections already in memory | Entity Framework Queries |

---

# What is IEnumerable?

`IEnumerable` is the basic interface used to iterate over a collection.

It works with objects that are already loaded into memory.

Examples include:

- List<T>
- Array
- HashSet
- Dictionary
- Queue
- Stack

---

# Example

```csharp
List<int> numbers = new List<int>
{
    1,2,3,4,5
};

IEnumerable<int> result =
    numbers.Where(x => x > 3);

foreach(var item in result)
{
    Console.WriteLine(item);
}
```

Output

```
4

5
```

---

# How IEnumerable Works

```
Database

↓

Load ALL Records

↓

Application Memory

↓

Where()

↓

Select()

↓

Result
```

Everything happens **inside the application**.

---

# Entity Framework Example

```csharp
IEnumerable<Employee> employees =
        context.Employees;

var result =
employees.Where(e => e.Age > 30);
```

Generated SQL

```sql
SELECT *
FROM Employees
```

Then

```
Application

↓

Loads Every Employee

↓

Filters Age > 30
```

Even if the table contains **1 million rows**, every row is first loaded into memory.

---

# Advantages of IEnumerable

- Easy to use
- Great for in-memory collections
- Supports foreach
- Suitable for small datasets

---

# Disadvantages of IEnumerable

- Loads all data into memory
- High memory usage
- Poor performance on large datasets
- Filtering happens after data is loaded

---

# What is IQueryable?

`IQueryable` is designed for querying remote data sources like SQL Server.

Instead of executing immediately, it builds an **Expression Tree**.

Entity Framework converts this expression tree into SQL.

---

# Example

```csharp
IQueryable<Employee> employees =
        context.Employees;

var result =
employees.Where(e => e.Age > 30);
```

Generated SQL

```sql
SELECT *
FROM Employees
WHERE Age > 30
```

Only matching records are returned.

---

# How IQueryable Works

```
Application

↓

Build LINQ Query

↓

Expression Tree

↓

Entity Framework

↓

SQL Query

↓

Database Executes Query

↓

Matching Records Returned
```

Filtering happens in SQL Server.

---

# Why IQueryable is Faster

Suppose

Employee Table

```
1,000,000 Records
```

You need only

```
Age > 30
```

### IEnumerable

```
Database

↓

1,000,000 Records

↓

Application

↓

Filter

↓

500 Records
```

Large amount of unnecessary data is transferred.

---

### IQueryable

```
Database

↓

WHERE Age > 30

↓

500 Records

↓

Application
```

Only required records are transferred.

---

# Deferred Execution

Both interfaces support Deferred Execution.

The query is **not executed** until you iterate over it.

```csharp
var query =
context.Employees
       .Where(e=>e.Age>30);

Console.WriteLine("Not Executed Yet");

foreach(var item in query)
{
}
```

Execution happens during

```
foreach

ToList()

First()

Single()

Count()

Any()
```

---

# IEnumerable Example

```csharp
var employees =
context.Employees
       .ToList();

var result =
employees.Where(e=>e.Age>30);
```

Execution

```
SQL

SELECT *

↓

Application

↓

Where()

↓

Result
```

---

# IQueryable Example

```csharp
var result =
context.Employees
       .Where(e=>e.Age>30);
```

Execution

```
SQL

SELECT *
FROM Employees
WHERE Age>30
```

Filtering occurs inside SQL Server.

---

# ToList() Changes Everything

Before

```csharp
var employees =
context.Employees;
```

Type

```
IQueryable
```

After

```csharp
var employees =
context.Employees.ToList();
```

Type

```
List<Employee>

↓

IEnumerable
```

Now every LINQ operation runs in memory.

---

# Practical Example

### Bad

```csharp
var employees =
context.Employees
       .ToList()
       .Where(e=>e.Salary>50000);
```

Flow

```
Database

↓

Loads Entire Table

↓

Application

↓

Filters Salary
```

---

### Good

```csharp
var employees =
context.Employees
       .Where(e=>e.Salary>50000)
       .ToList();
```

Flow

```
Database

↓

WHERE Salary>50000

↓

Only Matching Rows

↓

Application
```

Much faster.

---

# Performance Comparison

| Feature | IEnumerable | IQueryable |
|----------|-------------|------------|
| Memory Usage | High | Low |
| Database Calls | More Data Retrieved | Optimized |
| SQL Generated | No | Yes |
| Suitable For | Small Collections | Large Database Tables |
| Execution Location | Application | Database |
| Performance | Slower | Faster |

---

# When Should You Use IEnumerable?

Use `IEnumerable` when:

- Working with Lists
- Working with Arrays
- Data is already loaded
- Small collections
- No database involved

Examples

- List<Employee>
- Array
- Dictionary
- Queue

---

# When Should You Use IQueryable?

Use `IQueryable` when:

- Using Entity Framework
- Querying SQL Server
- Querying Oracle
- Querying PostgreSQL
- Working with large datasets
- Applying filtering, sorting and paging

---

# Common Interview Questions

## What is the biggest difference?

`IEnumerable` executes queries in memory.

`IQueryable` executes queries in the database.

---

## Which one is faster?

For database queries,

✅ IQueryable

because filtering happens in SQL Server.

---

## Does IEnumerable generate SQL?

No.

It works on objects already loaded into memory.

---

## Does IQueryable generate SQL?

Yes.

Entity Framework converts the LINQ Expression Tree into SQL.

---

## What happens after calling ToList()?

The query executes immediately.

The result is loaded into memory.

Further operations use `IEnumerable`.

---

## Can IQueryable become IEnumerable?

Yes.

Methods like

```csharp
ToList()

ToArray()

AsEnumerable()
```

materialize the query and switch processing to in-memory operations.

---

# Best Practices

✅ Use `IQueryable` when querying a database.

✅ Apply `Where()`, `OrderBy()`, `Skip()`, and `Take()` **before** calling `ToList()`.

✅ Call `ToList()` only when you actually need the data.

❌ Avoid calling `ToList()` too early.

---

# Summary

| Scenario | Recommended |
|----------|-------------|
| List<T> | IEnumerable |
| Array | IEnumerable |
| Entity Framework | IQueryable |
| SQL Server | IQueryable |
| Large Dataset | IQueryable |
| Small In-Memory Collection | IEnumerable |

---

# Interview One-Liner

- **IEnumerable** → Executes LINQ queries **in memory** after the data has been loaded.
- **IQueryable** → Builds an **expression tree** that Entity Framework translates into SQL, allowing the database to perform filtering, sorting, and paging before returning the results.