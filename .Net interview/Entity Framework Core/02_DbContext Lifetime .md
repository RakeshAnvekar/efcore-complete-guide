# Entity Framework Core – Module 7
# DbContext Lifetime

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 7 – DbContext Lifetime

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is DbContext?
2. What is Lifetime?
3. Why DbContext Lifetime Matters
4. DbContext Lifecycle
5. Scoped Lifetime
6. Singleton Lifetime
7. Transient Lifetime
8. Why DbContext is Scoped
9. Internal Working
10. Memory Diagram
11. Real-Time ASP.NET Core Example
12. Best Practices
13. Interview Questions
14. Summary
15. Key Takeaways

---

# What is DbContext?

DbContext is the main class of Entity Framework Core.

It acts as a bridge between your C# application and the database.

Example

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
}
```

Through DbContext we can

- Query data
- Insert data
- Update data
- Delete data
- Track changes
- Save changes

---

# What is Lifetime?

Lifetime means

```
How long an object lives in memory.
```

Example

```
Create Object

↓

Use Object

↓

Destroy Object
```

This duration is called the object's lifetime.

---

# Why Does DbContext Lifetime Matter?

DbContext contains

- Change Tracker
- Database Connection
- Entity States
- Original Values
- Current Values

If DbContext lives too long

```
Thousands of tracked entities

↓

High Memory Usage

↓

Performance Problems
```

If DbContext lives too short

```
Tracking Information Lost
```

Choosing the correct lifetime is very important.

---

# DbContext Lifecycle

```
HTTP Request Starts

↓

Create DbContext

↓

Execute Queries

↓

Track Entities

↓

SaveChanges()

↓

Dispose DbContext

↓

HTTP Request Ends
```

---

# ASP.NET Core Default Registration

```csharp
builder.Services.AddDbContext<AppDbContext>(options =>
{
    options.UseSqlServer(connectionString);
});
```

By default

```
Scoped
```

lifetime is used.

---

# What is Scoped Lifetime?

Scoped means

```
One DbContext

Per HTTP Request
```

Example

```
Request 1

↓

DbContext A

--------------------

Request 2

↓

DbContext B

--------------------

Request 3

↓

DbContext C
```

Each request gets its own DbContext.

---

# Memory Diagram

```
HTTP Request

↓

DbContext

↓

Change Tracker

↓

Database Connection

↓

Disposed
```

Nothing is shared between requests.

---

# Why Scoped?

Suppose User A

```
Updates Employee Salary
```

User B

```
Deletes Employee
```

Each request gets its own DbContext.

No conflicts.

No shared Change Tracker.

---

# What Happens if DbContext is Singleton?

Suppose

```
Singleton DbContext
```

Memory

```
Application

↓

One DbContext

↓

One Change Tracker

↓

All Users
```

Problems

```
User A

Tracks 500 Employees

↓

User B

Tracks 800 Employees

↓

User C

Tracks 200 Employees
```

Now

```
1500 Entities

Tracked
```

Huge memory.

---

# Another Problem

User A

```csharp
employee.Name = "Rakesh";
```

User B

```csharp
employee.Name = "Raj";
```

Same DbContext.

Race Condition.

Incorrect data.

---

# Is DbContext Thread Safe?

```
NO
```

Microsoft clearly states

```
DbContext

is NOT

Thread Safe.
```

Never share the same DbContext across multiple threads.

---

# What is Transient Lifetime?

Transient means

```
New DbContext

Every Injection
```

Example

```
Controller

↓

DbContext A

Service

↓

DbContext B

Repository

↓

DbContext C
```

Every class receives a different DbContext.

---

# Problem with Transient

Suppose

Repository

```csharp
employee.Name="Raj";
```

Service

```csharp
SaveChanges();
```

Different DbContexts.

Service cannot see Repository's tracked entity.

Update fails.

---

# Internal Working

```
HTTP Request

↓

Dependency Injection

↓

Create Scoped DbContext

↓

Execute Query

↓

Track Entities

↓

SaveChanges()

↓

Dispose DbContext
```

Everything happens inside a single request.

---

# Real-Time Example

Employee API

```
GET /employees/1
```

Flow

```
Request Starts

↓

Create DbContext

↓

Query Database

↓

Return Employee

↓

Dispose DbContext
```

Update API

```
PUT /employees/1
```

Flow

```
Request Starts

↓

Create DbContext

↓

Load Employee

↓

Modify Employee

↓

SaveChanges()

↓

Dispose DbContext
```

---

# Why Dispose DbContext?

DbContext holds

- Database Connection
- Change Tracker
- Memory

If not disposed

```
Memory Leak

Connection Leak

Performance Problems
```

---

# Scoped vs Singleton vs Transient

| Feature | Scoped | Singleton | Transient |
|----------|---------|-----------|-----------|
| Lifetime | One Request | Entire Application | Every Injection |
| Change Tracker | One Per Request | Shared | Multiple |
| Thread Safe | Yes (per request) | No | Yes |
| Recommended for EF | ✅ Yes | ❌ No | ⚠️ Rarely |

---

# Best Practices

✔ Register DbContext as Scoped.

✔ Never register DbContext as Singleton.

✔ Keep DbContext short-lived.

✔ Dispose DbContext after request.

✔ Never share DbContext between threads.

✔ One Unit of Work = One DbContext.

---

# Interview Questions

## What is DbContext Lifetime?

DbContext Lifetime defines how long a DbContext instance remains alive in memory.

---

## What is the default lifetime?

```
Scoped
```

---

## Why is DbContext Scoped?

Because one HTTP request should use one DbContext.

This provides

- One Change Tracker
- One Database Connection
- One Unit of Work

---

## Can DbContext be Singleton?

No.

Because

- Not Thread Safe
- Memory Growth
- Shared Change Tracker
- Concurrency Issues

---

## Is DbContext Thread Safe?

```
No
```

Never use the same DbContext instance across multiple threads.

---

## What happens if DbContext lives too long?

- More tracked entities
- Higher memory usage
- Slower DetectChanges()
- Performance degradation

---

## Why dispose DbContext?

To release

- Database connections
- Change Tracker
- Memory resources

---

# Summary

DbContext is the core component of Entity Framework Core and should have a short lifetime.

In ASP.NET Core, the recommended lifetime is **Scoped**, which creates one DbContext per HTTP request.

This ensures efficient memory usage, proper change tracking, and thread safety.

Avoid using Singleton DbContext because it causes memory leaks, shared state, and concurrency problems.

---

# Key Takeaways

✔ DbContext is not thread-safe.

✔ Default lifetime is Scoped.

✔ One HTTP Request = One DbContext.

✔ One DbContext = One Change Tracker.

✔ Dispose DbContext after each request.

✔ Never register DbContext as Singleton.

✔ Use Scoped for almost all web applications.

---

# Next Module

## Module 8 – DbContext Internals

Topics Covered

- DbContext Architecture
- DbSet Internals
- Query Pipeline
- Entity Materialization
- Change Tracker Integration
- SaveChanges Pipeline
- Database Connection Management
- Interview Questions