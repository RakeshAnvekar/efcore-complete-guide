<details>
<summary><h1>EF Core Fundamentals</h1></summary>
    
## What is Entity Framework Core (EF Core)?

Entity Framework Core (EF Core) is an ORM (Object Relational Mapper) for .NET applications.

It converts:

```text
C# Objects  ↔  SQL Database Tables
```

Instead of writing SQL queries manually like:

```sql
SELECT * FROM Employees
```

We can write LINQ queries:

```csharp
var employees = context.Employees.ToList();
```

EF Core internally converts LINQ queries into SQL queries.

---

# Why Companies Use EF Core

## Advantages of EF Core

- Faster development
- Less SQL writing
- Strong LINQ support
- Easy database migrations
- Database abstraction
- Easy CRUD operations
- Better maintainability
- Works well with ASP.NET Core

---

## Disadvantages of EF Core

- Can generate slow queries if used incorrectly
- Less control compared to pure SQL
- Performance tuning knowledge is required
- Complex queries may become inefficient
- Bulk operations are slower by default

---

# Interview Questions

## Q1: When should you avoid Entity Framework?

### Answer:

You should avoid EF Core in scenarios like:

- Complex high-performance queries
- Bulk insert/update/delete operations
- Very large-scale reporting systems
- Applications requiring maximum SQL optimization
- Real-time systems with extremely low latency

---

# Important Note

EF Core is excellent for:

- Business applications
- CRUD-heavy systems
- Rapid development

But for performance-critical systems, developers often combine:

- EF Core + Stored Procedures
- EF Core + Dapper
- Raw SQL Queries

</details>

---

<details>
<summary><h1>EF Core Architecture</h1></summary>


# Main Components of EF Core

EF Core mainly works using these components:

- DbContext
- Entity
- DbSet
- LINQ
- Change Tracker
- Database Provider

---

# 1. DbContext

## What is DbContext?

`DbContext` is the heart of EF Core.

It acts like:

- Database session
- Change tracker
- Unit of Work
- Bridge between C# code and database

---

## Why Do We Need DbContext?

Without `DbContext`, EF Core cannot:

- Connect to database
- Track entity changes
- Execute SQL queries
- Save data
- Manage transactions

It manages the complete communication between application and database.

---

## Real-Time Analogy

Think of `DbContext` like a:

```text
Manager between Application and SQL Server
```

Application talks to DbContext  
DbContext talks to Database

---

# Responsibilities of DbContext

## 1. Database Connection Management

Opens and closes database connection automatically.

---

## 2. Change Tracking

Tracks:

- Added entities
- Updated entities
- Deleted entities

Example:

```csharp
employee.Name = "Rakesh";
```

EF Core automatically detects changes.

---

## 3. Query Execution

Converts LINQ queries into SQL queries.

Example:

```csharp
var employees = context.Employees.ToList();
```

EF converts this into SQL internally.

---

## 4. Save Changes

`SaveChanges()` sends all tracked changes to database.

Example:

```csharp
context.SaveChanges();
```

---

## 5. Transaction Management

EF Core automatically handles transactions.

If one operation fails:

```text
Everything gets rolled back
```

---

# Example of DbContext

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
}
```

---

# Understanding the Above Code

## AppDbContext

Your custom database context class.

---

## DbContext

Base class provided by EF Core.

---

## DbSet<Employee>

Represents Employees table.

---

# 2. Entity

## What is an Entity?

Entity is a C# class that represents a database table.

Example:

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

This becomes:

```text
Employees Table
----------------
Id
Name
```

---

# Why Entities Are Needed?

Entities help EF Core map:

```text
C# Objects ↔ Database Tables
```

Without entities:

- EF Core cannot create tables
- EF Core cannot map data
- EF Core cannot track records

---

# Entity Example

## C# Object

```csharp
var emp = new Employee
{
    Id = 1,
    Name = "Rakesh"
};
```

## Database Row

| Id | Name   |
|----|--------|
| 1  | Rakesh |

---

# 3. DbSet

# What is DbSet?

`DbSet<T>` represents a table in database.

Example:

```csharp
public DbSet<Employee> Employees { get; set; }
```

Here:

```text
Employees = Database Table
Employee = Single Row
```

---

# Why Do We Need DbSet?

DbSet provides:

- Insert operations
- Update operations
- Delete operations
- Query operations

Without DbSet:

```text
EF Core cannot access table data
```

---

# Real-Time Analogy

Think of DbSet like:

```text
Collection/Table handler
```

It helps interact with a specific table.

---

# Common DbSet Operations

## Insert

```csharp
context.Employees.Add(employee);
```

---

## Read

```csharp
var employees = context.Employees.ToList();
```

---

## Update

```csharp
context.Employees.Update(employee);
```

---

## Delete

```csharp
context.Employees.Remove(employee);
```

---

# EF Core Architecture Flow

# Complete Flow Explanation

```text
Request
   ↓
DbContext
   ↓
LINQ Query
   ↓
EF Core Query Translator
   ↓
SQL Query
   ↓
SQL Server
   ↓
Results Returned
   ↓
C# Objects Created
```

---

# Step-by-Step Detailed Flow

## Step 1 — Application Request

User requests employee data.

Example:

```text
Get Employee By Id
```

---

## Step 2 — DbContext Receives Request

```csharp
var emp = context.Employees
```

DbContext starts building query.

---

## Step 3 — LINQ Query

```csharp
.Where(x => x.Id == 1)
.FirstOrDefault();
```

LINQ is database-independent query syntax.

---

## Step 4 — EF Core Converts LINQ to SQL

EF Core Query Translator converts:

```csharp
.Where(x => x.Id == 1)
```

Into:

```sql
WHERE Id = 1
```

---

# Final SQL Generated by EF Core

```sql
SELECT TOP(1) *
FROM Employees
WHERE Id = 1
```

---

## Step 5 — SQL Executes in SQL Server

SQL Server executes query.

---

## Step 6 — Data Returns to EF Core

Database returns row data.

Example:

| Id | Name   |
|----|--------|
| 1  | Rakesh |

---

## Step 7 — EF Core Converts Data to Objects

EF creates C# object:

```csharp
Employee emp = new Employee
{
    Id = 1,
    Name = "Rakesh"
};
```

---

# Full Example

## Entity

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

---

## DbContext

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
}
```

---

## LINQ Query

```csharp
var emp = context.Employees
                 .Where(x => x.Id == 1)
                 .FirstOrDefault();
```

---

## SQL Generated

```sql
SELECT TOP(1) *
FROM Employees
WHERE Id = 1
```

---

# Important Interview Questions

## Q1: Why is DbContext important?

### Answer:

DbContext manages:

- Database connection
- Query execution
- Change tracking
- Transactions
- Save operations

It is the core class of EF Core.

---

## Q2: Why do we use DbSet?

### Answer:

DbSet represents a database table and provides CRUD operations.

Without DbSet, EF Core cannot query or manipulate table data.

---

## Q3: What is Change Tracking?

### Answer:

EF Core automatically tracks entity changes and updates only modified data during `SaveChanges()`.

---

## Q4: How does EF Core work internally?

### Answer:

```text
LINQ Query → EF Core → SQL Query → SQL Server → Result → C# Objects
```

---

# Key Takeaways

| Component | Purpose |
|-----------|----------|
| DbContext | Main database manager |
| Entity | Represents database table |
| DbSet | Represents table operations |
| LINQ | Query language |
| SaveChanges | Commits changes to DB |
| Change Tracker | Detects modifications |

---
<details>
<summary><h1>5. Types of Approaches</h1></summary>

# 5. Types of Approaches

# Code First

You create C# classes first.

EF creates database.

MOST USED in modern projects.

---

## How Code First Works

```text
C# Classes → EF Core → Database Tables
```

Developer creates:

- Entities
- DbContext
- Relationships

EF Core automatically creates database tables.

---

## Advantages of Code First

- Faster development
- Full control in C#
- Easy migrations
- Best for Agile development
- MOST USED in modern .NET projects

---

## Example

### Entity Class

```csharp
public class Employee
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

---

### DbContext

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
}
```

---

### Migration Command

```bash
dotnet ef migrations add InitialCreate
```

EF Core creates database automatically.

---

# Database First

Database already exists.

EF generates models.

Mostly used in legacy enterprise apps.

---

## How Database First Works

```text
Existing Database → EF Core → C# Models
```

EF Core reads database schema and generates:

- Entity classes
- DbContext
- Relationships

---

## Advantages of Database First

- Best for existing databases
- Useful for legacy enterprise systems
- Easy integration with old applications
- Database already designed by DBAs

---

## Example Command

```bash
Scaffold-DbContext
```

or

```bash
dotnet ef dbcontext scaffold
```

This generates C# classes from database tables.

---

# Difference Between Code First and Database First

| Feature | Code First | Database First |
|----------|-------------|----------------|
| Database Creation | From C# Classes | Existing Database |
| Flexibility | High | Medium |
| Modern Projects | Mostly Used | Less Used |
| Legacy Projects | Rare | Common |
| Migrations | Easy | Limited |

---

# Interview Question

## Q1: Which approach is mostly used in modern projects?

### Answer:

```text
Code First
```

Because it provides:

- Faster development
- Better maintainability
- Easy migrations
- Full control in C#

</details>

---

<details>
<summary><h1>Install EF Core Packages</h1></summary>

# Install EF Core Packages

## Packages

Install:

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

# 1. Microsoft.EntityFrameworkCore

## Why Do We Need This Package?

This is the main EF Core package.

It contains:

- DbContext
- DbSet
- LINQ support
- Change tracking
- ORM functionality

Without this package:

```text
EF Core cannot work
```

---

## Example

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }
}
```

`DbContext` and `DbSet` come from this package.

---

# 2. Microsoft.EntityFrameworkCore.SqlServer

## Why Do We Need This Package?

EF Core itself is database independent.

This package acts as:

```text
SQL Server Provider
```

It helps EF Core:

- Connect to SQL Server
- Generate SQL Server queries
- Execute SQL operations

---

## Example

```csharp
options.UseSqlServer(connectionString);
```

`UseSqlServer()` comes from this package.

---

# 3. Microsoft.EntityFrameworkCore.Tools

## Why Do We Need This Package?

This package provides migration and CLI tools.

Used for:

- Creating migrations
- Updating database
- Removing migrations
- Scaffolding

---

## Important Commands

### Create Migration

```bash
dotnet ef migrations add InitialCreate
```

---

### Update Database

```bash
dotnet ef database update
```

---

### Remove Migration

```bash
dotnet ef migrations remove
```

---

# Summary Table

| Package | Purpose |
|----------|----------|
| Microsoft.EntityFrameworkCore | Main EF Core functionality |
| Microsoft.EntityFrameworkCore.SqlServer | SQL Server provider |
| Microsoft.EntityFrameworkCore.Tools | Migration & CLI tools |

---

# Key Takeaways

```text
EntityFrameworkCore         → Main Engine
SqlServer Package           → SQL Server Connector
Tools Package               → Migration Manager
```

</details>

</details>
<details>
   ---

# DbContext Lifecycle

Lifecycle means:

```text
When DbContext is created → used → disposed
```

---

# Lifecycle Flow

```text
Context created
      ↓
Query executed
      ↓
Tracks entities
      ↓
SaveChanges()
      ↓
Context disposed
```

---

# In ASP.NET Core

Normally:

```csharp
services.AddDbContext<AppDbContext>();
```

---

# Default Lifetime

```text
Scoped
```

Meaning:

```text
One DbContext per HTTP request
```

---

# Example

## Request 1

```text
Create DbContext
Use it
Dispose it
```

---

## Request 2

```text
Create new DbContext
Use it
Dispose it
```

---

# Why Scoped is Best

Because:

- Safe
- Efficient
- Prevents threading issues
- Better memory management

---

# Interview Question

## Why DbContext should NOT be Singleton?

### Answer:

Because:

- DbContext is NOT thread-safe
- Change tracker becomes huge
- Memory issues occur
- Multiple users share same context
- Data corruption risks increase

---

# What SaveChanges() Does

## Example

```csharp
context.Employees.Add(employee);

context.SaveChanges();
```

---

# SaveChanges() Internally

`SaveChanges()`:

- Detects changes
- Generates SQL
- Opens DB connection
- Executes SQL
- Commits transaction
- Updates tracking states

---

# Does SaveChanges() Save Immediately?

```text
YES
```

Until `SaveChanges()`:

```text
Changes only exist in memory
```

---

# Example

```csharp
var emp = new Employee
{
    Name = "Rakesh"
};

context.Employees.Add(emp);
```

Still NOT in database.

Only after:

```csharp
context.SaveChanges();
```

---

# Multiple Operations

```csharp
context.Employees.Add(emp1);

context.Employees.Add(emp2);

context.SaveChanges();
```

EF sends both operations in a single transaction.

---

# Performance Tip

## BAD

```csharp
foreach(var emp in employees)
{
    context.Employees.Add(emp);

    context.SaveChanges();
}
```

---

## Why BAD?

```text
Database hit happens every iteration
```

This reduces performance.

---

# GOOD

```csharp
context.Employees.AddRange(employees);

context.SaveChanges();
```

---

# Why GOOD?

Because:

- Single database transaction
- Better performance
- Fewer DB calls
- Faster execution

---

# 3. Change Tracking Basics

```text
VERY IMPORTANT TOPIC
```

---

# What is Change Tracking?

EF Core tracks entity changes automatically.

---

# Example

```csharp
var emp = context.Employees.First();

emp.Name = "New Name";
```

EF remembers:

```text
Old Name → New Name
```

---

# Why Tracking Exists

So EF knows:

- What changed
- What SQL to generate
- Which columns to update

---

# Without Tracking

```text
EF cannot update automatically
```

---

# Example SQL Generated

```sql
UPDATE Employees
SET Name = 'New Name'
WHERE Id = 1
```

---

# Important Interview Questions

## Q1: What is DbContext Lifecycle?

### Answer:

```text
Create → Use → Track Changes → SaveChanges → Dispose
```

---

## Q2: Why is DbContext Scoped?

### Answer:

Because one DbContext per request is:

- Safe
- Efficient
- Thread-safe
- Easy to manage

---

## Q3: What happens before SaveChanges()?

### Answer:

Changes exist only inside memory and change tracker.

Database is not updated until `SaveChanges()` executes.

---

## Q4: What is Change Tracking?

### Answer:

EF Core automatically monitors entity changes and generates required SQL updates.

---
</details>

<details>
    # Entity Framework Core — Change Tracker Deep Dive

# What is Change Tracker?

Change Tracker is an internal mechanism inside Entity Framework Core that tracks entity changes in memory.

It helps EF Core identify:

- What changed
- Which entity changed
- Which SQL query to generate
- Whether to INSERT, UPDATE, or DELETE

---

# Why Change Tracker Exists

Without Change Tracker, EF Core would not know:

- Which entity was modified
- Which columns changed
- Which SQL statement should execute

Change Tracker automatically monitors entity states.

---

# Where Change Tracker Exists

Change Tracker exists inside:

```csharp
DbContext
```

Every DbContext has:

```csharp
context.ChangeTracker
```

---

# Basic Flow

```text
Load Entity
    ↓
Entity Tracked
    ↓
Modify Entity
    ↓
State Changes
    ↓
SaveChanges()
    ↓
SQL Generated
```

---

# Example

## Step 1 — Load Entity

```csharp
var emp = context.Employees
                 .First(x => x.Id == 1);
```

Entity state:

```text
Unchanged
```

EF stores:
- Original values
- Current values

---

## Step 2 — Modify Entity

```csharp
emp.Name = "Rakesh";
```

Now EF detects:

```text
Old Value = OldName
New Value = Rakesh
```

Entity state becomes:

```text
Modified
```

---

## Step 3 — SaveChanges

```csharp
context.SaveChanges();
```

Generated SQL:

```sql
UPDATE Employees
SET Name = 'Rakesh'
WHERE Id = 1
```

After save:

```text
Unchanged
```

---

# Entity States

EF Core tracks 5 main states.

| State | Meaning |
|---|---|
| Added | New entity |
| Modified | Existing entity updated |
| Deleted | Entity marked for deletion |
| Unchanged | No modifications |
| Detached | Entity not tracked |

---

# Added State

Example:

```csharp
context.Employees.Add(emp);
```

State:

```text
Added
```

Generated SQL:

```sql
INSERT INTO Employees
```

---

# Modified State

Example:

```csharp
emp.Name = "ABC";
```

State:

```text
Modified
```

Generated SQL:

```sql
UPDATE Employees
```

---

# Deleted State

Example:

```csharp
context.Employees.Remove(emp);
```

State:

```text
Deleted
```

Generated SQL:

```sql
DELETE FROM Employees
```

---

# Unchanged State

Example:

```csharp
var emp = context.Employees.First();
```

State:

```text
Unchanged
```

Meaning:
- Entity loaded
- No modifications

---

# Detached State

Example:

```csharp
context.Entry(emp).State = EntityState.Detached;
```

Meaning:

```text
Entity is NOT tracked by EF
```

---

# How Change Detection Works

EF Core stores:
- Original values
- Current values

During SaveChanges(), EF compares both values.

Example:

```text
Original Name = Raj
Current Name = Rakesh
```

Result:

```text
Modified
```

---

# SaveChanges and Change Tracker

SaveChanges():

1. Detects changes
2. Generates SQL
3. Executes SQL
4. Updates entity states

---

# AsNoTracking()

Very important for performance optimization.

Example:

```csharp
var employees = context.Employees
                       .AsNoTracking()
                       .ToList();
```

Meaning:

```text
Do NOT track entities
```

---

# Benefits of AsNoTracking

Advantages:
- Faster queries
- Less memory usage
- Better performance

Best for:
- Read-only APIs
- Reports
- Dashboard screens

---

# When NOT to Use AsNoTracking

Bad example:

```csharp
var emp = context.Employees
                 .AsNoTracking()
                 .First();

emp.Name = "ABC";

context.SaveChanges();
```

Problem:
- EF does not detect changes
- No UPDATE query generated

Reason:
- Entity is not tracked

---

# Attach()

Used when entity comes from external source.

Example:

```csharp
var emp = new Employee
{
    Id = 1,
    Name = "Updated"
};

context.Attach(emp);
```

Now EF starts tracking entity.

---

# Entry()

Used to inspect entity state.

Example:

```csharp
var state = context.Entry(emp).State;
```

Possible values:
- Added
- Modified
- Deleted
- Unchanged
- Detached

---

# ChangeTracker API

Inspect tracked entities:

```csharp
context.ChangeTracker.Entries()
```

Useful for:
- Debugging
- Logging
- Auditing

---

# Performance Considerations

Tracking consumes:
- Memory
- CPU

Why?
Because EF stores:
- Snapshots
- Property values
- Entity states

---

# Performance Problem Example

```csharp
var employees = context.Employees.ToList();
```

Problem:
- All entities tracked
- High memory usage

Better:

```csharp
var employees = context.Employees
                       .AsNoTracking()
                       .ToList();
```

---

# Best Practices

## Rule 1
Keep DbContext short-lived.

---

## Rule 2
Use AsNoTracking for read-only operations.

---

## Rule 3
Avoid loading huge tracked datasets.

---

## Rule 4
Use tracking only when update/delete is required.

---

# Common Interview Questions

## Q1. What is Change Tracker?

Tracks entity changes and states inside DbContext.

---

## Q2. Why tracking impacts performance?

Because EF stores snapshots and state information.

---

## Q3. Difference between tracked and non-tracked entities?

| Tracked | Non-tracked |
|---|---|
| Change detection enabled | No change detection |
| Higher memory usage | Lower memory usage |
| Supports update | Mostly read-only |
| Slower | Faster |

---

## Q4. Why use AsNoTracking()?

Improves performance for read-only operations.

---

## Q5. What is Detached state?

Entity ignored by DbContext.

---

# Real World Scenario

Suppose dashboard API loads:

```text
50000 records
Read-only data
```

Bad:

```csharp
context.Employees.ToListAsync();
```

Good:

```csharp
context.Employees
       .AsNoTracking()
       .ToListAsync();
```

Benefits:
- Faster response
- Lower memory usage
- Better scalability

---

# Summary

Change Tracker:
- Tracks entities
- Detects changes
- Manages states
- Helps generate SQL automatically
</details>
<details>
    # Entity Framework Core — LINQ Deep Dive

# What is LINQ?

LINQ stands for:

```text
Language Integrated Query
```

LINQ allows querying data using C# syntax.

Example:

```csharp
var employees = context.Employees
                       .Where(x => x.Salary > 50000)
                       .ToList();
```

Entity Framework Core converts LINQ into SQL.

Generated SQL:

```sql
SELECT *
FROM Employees
WHERE Salary > 50000
```

---

# Why LINQ is Important

LINQ is the core of EF Core querying.

EF Core works as:

```text
LINQ → Expression Tree → SQL Query
```

Without LINQ:
- Querying becomes difficult
- EF Core cannot generate SQL properly

LINQ is one of the most important interview topics.

---

# LINQ Execution Flow

```text
LINQ Query
    ↓
Expression Tree
    ↓
EF Core
    ↓
SQL Query
    ↓
Database
```

---

# Common LINQ Methods

# 1. Where()

Used for filtering data.

Example:

```csharp
var employees = context.Employees
                       .Where(x => x.Salary > 50000)
                       .ToList();
```

Generated SQL:

```sql
SELECT *
FROM Employees
WHERE Salary > 50000
```

---

# 2. Select()

Very important for performance optimization.

Used to fetch only required columns.

---

# Bad Example

```csharp
var employees = context.Employees
                       .ToList();
```

Problem:
- Loads all columns
- High memory usage
- Poor performance

---

# Good Example

```csharp
var employees = context.Employees
                       .Select(x => new
                       {
                           x.Id,
                           x.Name
                       })
                       .ToList();
```

Generated SQL:

```sql
SELECT Id, Name
FROM Employees
```

---

# Why Select() is Important

Suppose a table contains:
- 50 columns
- Images
- Large text fields

Without Select():
- Huge unnecessary data gets loaded

Select() improves:
- Performance
- Memory usage
- API response time

---

# 3. OrderBy()

Used for sorting.

Example:

```csharp
var employees = context.Employees
                       .OrderBy(x => x.Name)
                       .ToList();
```

Generated SQL:

```sql
ORDER BY Name
```

---

# Descending Order

```csharp
.OrderByDescending(x => x.Salary)
```

---

# 4. FirstOrDefault()

Returns first matching record.

Example:

```csharp
var emp = context.Employees
                 .FirstOrDefault(x => x.Id == 1);
```

Generated SQL:

```sql
SELECT TOP(1) *
FROM Employees
WHERE Id = 1
```

---

# 5. SingleOrDefault()

Expects exactly one record.

Example:

```csharp
var emp = context.Employees
                 .SingleOrDefault(x => x.Email == email);
```

---

# Difference Between FirstOrDefault() and SingleOrDefault()

| FirstOrDefault | SingleOrDefault |
|---|---|
| Returns first match | Expects exactly one match |
| No exception for duplicates | Throws exception if duplicates exist |
| Faster | Slightly slower |

---

# When to Use SingleOrDefault()

Use when data must be unique:
- Email
- Aadhaar
- EmployeeCode

---

# 6. Any()

Used to check existence.

Example:

```csharp
var exists = context.Employees
                    .Any(x => x.Id == 1);
```

Generated SQL uses:

```sql
EXISTS
```

---

# Why Any() is Better than Count() > 0

Bad:

```csharp
context.Employees.Count() > 0
```

Good:

```csharp
context.Employees.Any()
```

Reason:
- Any() stops after first match
- Better performance

---

# 7. Count()

Used for total count.

Example:

```csharp
var total = context.Employees.Count();
```

Generated SQL:

```sql
COUNT(*)
```

---

# 8. Take()

Used to limit records.

Example:

```csharp
var employees = context.Employees
                       .Take(10)
                       .ToList();
```

Generated SQL:

```sql
TOP(10)
```

---

# 9. Skip()

Used for pagination.

Example:

```csharp
.Skip(10)
.Take(10)
```

---

# Pagination Example

```csharp
var employees = context.Employees
                       .OrderBy(x => x.Id)
                       .Skip(10)
                       .Take(10)
                       .ToList();
```

Generated SQL:

```sql
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY
```

---

# Deferred Execution

Very important interview topic.

---

# What is Deferred Execution?

LINQ query executes only when result is needed.

Example:

```csharp
var query = context.Employees
                   .Where(x => x.Salary > 50000);
```

At this point:
- SQL query has NOT executed yet

---

# Query Executes Here

```csharp
var result = query.ToList();
```

Now SQL executes.

---

# Benefits of Deferred Execution

Advantages:
- Dynamic query building
- Better optimization
- Query composition

---

# Immediate Execution

Methods causing immediate execution:
- ToList()
- FirstOrDefault()
- Count()
- Any()

These hit the database immediately.

---

# IQueryable vs IEnumerable

One of the most important interview topics.

---

# IQueryable

```csharp
IQueryable<Employee>
```

Meaning:
- Query not executed yet
- SQL generation still possible
- Filtering happens in database

---

# IEnumerable

```csharp
IEnumerable<Employee>
```

Meaning:
- Data already loaded into memory
- Filtering happens in application memory

---

# Bad Example

```csharp
var employees = context.Employees.ToList();

employees.Where(x => x.Salary > 50000);
```

Problem:
- Loads all data first
- Filtering happens in memory
- Poor performance

---

# Good Example

```csharp
var employees = context.Employees
                       .Where(x => x.Salary > 50000)
                       .ToList();
```

Filtering happens in SQL Server.

---

# Golden Rule

```text
Keep IQueryable as long as possible
```

---

# Async LINQ Queries

Very important for ASP.NET Core APIs.

---

# ToListAsync()

```csharp
var employees = await context.Employees
                             .ToListAsync();
```

---

# FirstOrDefaultAsync()

```csharp
var emp = await context.Employees
                       .FirstOrDefaultAsync(
                           x => x.Id == 1);
```

---

# Why Async is Important

Without async:
- Thread blocks while waiting for DB

With async:
- Better scalability
- Better throughput
- Better API performance

---

# Common Interview Questions

# Q1. Difference between IQueryable and IEnumerable?

| IQueryable | IEnumerable |
|---|---|
| Executes in DB | Executes in memory |
| Better performance | Poor for huge datasets |
| Supports SQL translation | No SQL translation |

---

# Q2. What is Deferred Execution?

Query executes only when result is required.

---

# Q3. Why Select() improves performance?

Because only required columns are fetched.

---

# Q4. Why Any() is better than Count() > 0?

Any() stops after first matching record.

---

# Q5. Why early ToList() is dangerous?

Because it loads huge unnecessary data into memory.

---

# Best Practices

## Rule 1
Use Select() whenever possible.

---

## Rule 2
Avoid early ToList().

---

## Rule 3
Keep IQueryable as long as possible.

---

## Rule 4
Use async methods in APIs.

---

## Rule 5
Use pagination for huge datasets.

---

# Real World Performance Example

Bad:

```csharp
var employees = context.Employees
                       .ToList()
                       .Where(x => x.Salary > 50000);
```

Problem:
- Entire table loaded into memory

---

# Better Approach

```csharp
var employees = context.Employees
                       .Where(x => x.Salary > 50000)
                       .ToList();
```

Filtering happens inside SQL Server.

Benefits:
- Faster queries
- Less memory usage
- Better scalability

---

# Summary

# Entity Framework Core — Performance Optimization Notes

# EF Core Performance Best Practices

Performance optimization is one of the most important topics in Entity Framework Core interviews and real-world applications.

---

# 1. Use AsNoTracking() for Read-Only Queries

Use `AsNoTracking()` when data is only being read and not updated.

Example:

```csharp
var employees = await context.Employees
                             .AsNoTracking()
                             .ToListAsync();
```

---

# Why AsNoTracking() Improves Performance

Without `AsNoTracking()`:
- EF Core tracks every entity
- Stores snapshots
- Tracks property changes
- Uses more memory and CPU

With `AsNoTracking()`:
- No change tracking
- Less memory usage
- Faster query execution

---

# Best Use Cases

Use for:
- GET APIs
- Reports
- Dashboard screens
- Read-only data

---

# Avoid

Do NOT use `AsNoTracking()` if entity needs update later.

Bad Example:

```csharp
var emp = await context.Employees
                       .AsNoTracking()
                       .FirstAsync();

emp.Name = "Updated";

await context.SaveChangesAsync();
```

Problem:
- EF does not track entity
- Changes are not saved

---

# 2. Keep IQueryable as Long as Possible

Very important optimization rule.

---

# Bad Example

```csharp
var employees = context.Employees.ToList();

var filtered = employees
               .Where(x => x.Salary > 50000);
```

Problem:
- Entire table loaded into memory
- Filtering happens in application memory
- Poor performance

---

# Good Example

```csharp
var employees = context.Employees
                       .Where(x => x.Salary > 50000)
                       .ToList();
```

Benefits:
- Filtering happens in SQL Server
- Less memory usage
- Faster query execution

---

# Why IQueryable is Important

`IQueryable`:
- Delays execution
- Allows SQL translation
- Performs filtering in database

Golden Rule:

```text
Keep IQueryable as long as possible
```

---

# 3. Use Select() to Fetch Only Required Columns

Very important optimization technique.

---

# Bad Example

```csharp
var employees = await context.Employees
                             .ToListAsync();
```

Problem:
- Loads all columns
- High memory usage
- Slow queries for large tables

---

# Good Example

```csharp
var employees = await context.Employees
                             .Select(x => new
                             {
                                 x.Id,
                                 x.Name
                             })
                             .ToListAsync();
```

Generated SQL:

```sql
SELECT Id, Name
FROM Employees
```

---

# Why Select() Improves Performance

Suppose table contains:
- 50 columns
- Images
- Large text fields

Without `Select()`:
- Huge unnecessary data loaded

With `Select()`:
- Only required columns fetched

Benefits:
- Lower memory usage
- Faster API response
- Better scalability

---

# 4. Use Pagination for Huge Datasets

Never load huge datasets at once.

---

# Bad Example

```csharp
var employees = await context.Employees
                             .ToListAsync();
```

Problem:
- Loads all records
- High memory usage
- Slow API response

---

# Good Example

```csharp
var employees = await context.Employees
                             .OrderBy(x => x.Id)
                             .Skip(0)
                             .Take(10)
                             .ToListAsync();
```

Generated SQL:

```sql
OFFSET 0 ROWS
FETCH NEXT 10 ROWS ONLY
```

---

# Benefits of Pagination

Advantages:
- Faster response
- Less memory usage
- Better user experience
- Better scalability

---

# Real API Example

```http
GET /api/employees?page=1&pageSize=10
```

---

# Combined Best Practice Example

```csharp
var employees = await context.Employees
                             .AsNoTracking()
                             .Where(x => x.IsActive)
                             .Select(x => new
                             {
                                 x.Id,
                                 x.Name,
                                 x.Email
                             })
                             .OrderBy(x => x.Name)
                             .Skip(0)
                             .Take(10)
                             .ToListAsync();
```

This is:
- Optimized
- Read-only
- Paginated
- Minimal column selection
- Efficient SQL generation

---

# Common Interview Questions

## Q1. Why use AsNoTracking()?

Improves read query performance by disabling change tracking.

---

## Q2. Why keep IQueryable as long as possible?

Because filtering executes in database instead of application memory.

---

## Q3. Why Select() improves performance?

Only required columns are fetched.

---

## Q4. Why pagination is important?

Prevents loading huge datasets into memory.

---

# Golden Performance Rules

## Rule 1
Use `AsNoTracking()` for read-only operations.

---

## Rule 2
Avoid early `ToList()`.

---

## Rule 3
Keep `IQueryable` alive as long as possible.

---

## Rule 4
Use `Select()` to fetch only needed columns.

---

## Rule 5
Always paginate large datasets.

---

# Summary

Most important EF Core performance optimizations:
- AsNoTracking()
- IQueryable
- Select()
- Pagination
</details>

