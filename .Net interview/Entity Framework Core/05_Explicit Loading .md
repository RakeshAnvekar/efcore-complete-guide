# Entity Framework Core – Module 11
# Explicit Loading

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 11 – Explicit Loading

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is Explicit Loading?
2. Why Do We Need Explicit Loading?
3. Real-Time Example
4. Internal Working
5. Collection().Load()
6. Reference().Load()
7. Memory Diagram
8. SQL Generated
9. Performance
10. Advantages
11. Disadvantages
12. Lazy vs Eager vs Explicit Loading
13. Best Practices
14. Interview Questions
15. Summary
16. Key Takeaways

---

# What is Explicit Loading?

Explicit Loading is a loading strategy where the **developer manually loads related entities whenever required**.

Unlike Lazy Loading,

Entity Framework **does not automatically load** related entities.

Unlike Eager Loading,

Entity Framework **does not load related entities with the initial query**.

Instead,

the developer decides exactly when related data should be loaded.

---

# Why Do We Need Explicit Loading?

Suppose we have

```
Department

↓

Employees
```

Initially,

the application only needs

```
Department Name
```

Later,

the user clicks

```
View Employees
```

Instead of loading Employees during the first query,

we load them only when the user requests them.

This is Explicit Loading.

---

# Real-Time Example

Department Table

| Id | Name |
|----|------|
|1|IT|

Employee Table

| Id | Name | DepartmentId |
|----|------|--------------|
|1|Rakesh|1|
|2|Raj|1|

---

# Step 1 – Load Department

```csharp
using var context = new AppDbContext();

var department = context.Departments.First();
```

Generated SQL

```sql
SELECT *
FROM Departments;
```

Memory

```
Department

↓

IT

Employees

NOT Loaded
```

---

# Step 2 – Load Employees Later

Now suppose the user wants to see employees.

We manually tell Entity Framework

```csharp
context.Entry(department)
       .Collection(d => d.Employees)
       .Load();
```

Generated SQL

```sql
SELECT *
FROM Employees
WHERE DepartmentId = 1;
```

Now

```
Department

↓

Employees

↓

Loaded
```

---

# Why is it Called Explicit Loading?

Because the developer explicitly says

```
Load related data now.
```

Entity Framework never loads it automatically.

---

# Internal Working

```
Application

↓

Load Department

↓

Employees Not Loaded

↓

Developer Calls Load()

↓

Entity Framework Executes SQL

↓

Employees Loaded

↓

Stored in Change Tracker
```

---

# Collection().Load()

Use Collection().Load() for

```
One-to-Many

Many-to-Many
```

relationships.

Example

```csharp
public class Department
{
    public ICollection<Employee> Employees { get; set; }
}
```

Load Employees

```csharp
context.Entry(department)
       .Collection(d => d.Employees)
       .Load();
```

Generated SQL

```sql
SELECT *
FROM Employees
WHERE DepartmentId = 1;
```

---

# Reference().Load()

Use Reference().Load() for

```
One-to-One

Many-to-One
```

relationships.

Example

```text
Employee

↓

Department
```

Entity

```csharp
public class Employee
{
    public Department Department { get; set; }
}
```

Load Department

```csharp
context.Entry(employee)
       .Reference(e => e.Department)
       .Load();
```

Generated SQL

```sql
SELECT *
FROM Departments
WHERE Id = 1;
```

---

# Memory Diagram

Before Load()

```
STACK

department

↓

Reference

-----------------------

HEAP

Department

Employees

NULL
```

After Load()

```
STACK

department

↓

Reference

-----------------------

HEAP

Department

↓

Employees

↓

Rakesh

Raj
```

---

# SQL Execution

Initial Query

```sql
SELECT *
FROM Departments;
```

Later

```csharp
context.Entry(department)
       .Collection(d => d.Employees)
       .Load();
```

Generated SQL

```sql
SELECT *
FROM Employees
WHERE DepartmentId = 1;
```

Notice

The SQL is executed only when Load() is called.

---

# Real-Time Banking Example

Relationship

```
Customer

↓

Accounts
```

Initially

```csharp
var customer =
context.Customers.First();
```

Only Customer is loaded.

Later

```csharp
context.Entry(customer)
       .Collection(c => c.Accounts)
       .Load();
```

Accounts are now loaded.

---

# Performance

Explicit Loading executes SQL only when requested.

However,

calling Load() repeatedly inside a loop can create multiple SQL queries.

Example

```csharp
foreach(var department in departments)
{
    context.Entry(department)
           .Collection(d => d.Employees)
           .Load();
}
```

Suppose

```
100 Departments
```

Queries executed

```
1 Department Query

+

100 Employee Queries

=

101 Queries
```

This becomes another example of the

```
N+1 Query Problem
```

---

# Advantages

✔ Complete control over loading.

✔ Loads related data only when required.

✔ No hidden SQL queries.

✔ Easy to understand when SQL executes.

✔ Good for conditional loading.

---

# Disadvantages

❌ More code.

❌ Can create N+1 Query Problem.

❌ Developer must remember to call Load().

❌ Not suitable for loading many related entities in loops.

---

# Lazy vs Eager vs Explicit Loading

| Feature | Lazy Loading | Eager Loading | Explicit Loading |
|----------|--------------|---------------|------------------|
| Related Data Loaded | Automatically when accessed | Immediately | Only when Load() is called |
| SQL Generated | Automatic | Initial Query | Manual |
| Uses Include() | ❌ | ✔ | ❌ |
| Uses Load() | ❌ | ❌ | ✔ |
| Uses Proxy | ✔ | ❌ | ❌ |
| Developer Controls Loading | ❌ | ❌ | ✔ |
| Can Cause N+1 | ✔ | ❌ | ✔ (if misused) |

---

# When Should We Use Explicit Loading?

Use Explicit Loading when

- Related data is optional.
- Related data depends on user interaction.
- You want full control over SQL execution.
- You don't want to load unnecessary data initially.

Examples

- User clicks **View Details**
- Expand/Collapse sections
- Optional child information
- Admin screens

---

# Best Practices

✔ Use Collection().Load() for collections.

✔ Use Reference().Load() for single navigation properties.

✔ Avoid calling Load() inside loops.

✔ Prefer Eager Loading when you already know related data is required.

✔ Monitor generated SQL using logging.

---

# Interview Questions

## What is Explicit Loading?

Explicit Loading is a technique where the developer manually loads related entities using Load().

---

## Which methods are used?

For collections

```csharp
Collection().Load()
```

For reference navigation

```csharp
Reference().Load()
```

---

## Does Explicit Loading automatically execute SQL?

No.

SQL is executed only when the developer calls Load().

---

## Can Explicit Loading cause the N+1 Query Problem?

Yes.

Calling Load() inside a loop may generate one SQL query per parent entity.

---

## When should Explicit Loading be used?

Use Explicit Loading when related data is required only under specific conditions and you want full control over when SQL queries are executed.

---

# Summary

Explicit Loading allows developers to manually load related entities whenever required.

Unlike Lazy Loading, Entity Framework never loads related data automatically.

Unlike Eager Loading, related data is not retrieved during the initial query.

Instead, the developer explicitly calls Collection().Load() or Reference().Load() to load navigation properties.

This provides complete control over database access but should be used carefully to avoid generating excessive SQL queries.

---

# Key Takeaways

✔ Explicit Loading is manual loading.

✔ Uses Collection().Load() for collections.

✔ Uses Reference().Load() for single entities.

✔ Gives complete control over SQL execution.

✔ No automatic loading.

✔ Can still cause N+1 if Load() is used inside loops.

✔ Best for conditionally loading related data.

---

# Easy Trick to Remember

Imagine ordering food in a restaurant.

### Lazy Loading

```
You ask for dessert.

↓

Waiter automatically brings it.
```

---

### Eager Loading

```
You order

Main Course

+

Dessert

↓

Everything arrives together.
```

---

### Explicit Loading

```
You order

Main Course

↓

Finish Eating

↓

Call Waiter

↓

"Please bring Dessert."

↓

Dessert arrives.
```

You explicitly requested it.

---

# Next Module

## Module 12 – Advanced Loading & Query Optimization

Topics Covered

- Filtered Include
- Split Queries
- Single Query vs Split Query
- Identity Resolution
- Query Performance
- Projection using Select()
- Avoiding Cartesian Explosion
- Best Practices
- Interview Questions
```