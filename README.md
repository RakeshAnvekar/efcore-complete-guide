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

</details>
