# SQL Server Temp Tables – Complete Interview Notes

## Overview

A **Temp Table** is a temporary table created inside SQL Server's **TempDB** database. It behaves like a normal table but exists only for a limited lifetime.

Temp Tables are commonly used to:

* Store intermediate results
* Simplify complex queries
* Improve performance
* Reduce repeated calculations
* Break large queries into smaller steps

---

# Why Do We Need Temp Tables?

Suppose a report requires joining five large tables.

Without a Temp Table:

```sql
SELECT ...
FROM Orders o
JOIN Customers c ON o.CustomerId = c.CustomerId
JOIN OrderItems oi ON o.OrderId = oi.OrderId
JOIN Products p ON oi.ProductId = p.ProductId
JOIN Categories cat ON p.CategoryId = cat.CategoryId;
```

If this query is used multiple times, SQL Server repeats the entire operation.

Instead:

```sql
SELECT
    o.OrderId,
    c.CustomerName,
    oi.ProductId,
    oi.Quantity
INTO #Orders
FROM Orders o
JOIN Customers c ON o.CustomerId = c.CustomerId
JOIN OrderItems oi ON o.OrderId = oi.OrderId;
```

Now every subsequent query reads from **#Orders** instead of rejoining the base tables.

---

# Where Are Temp Tables Stored?

All Temp Tables are stored in:

```
TempDB
```

Flow:

```
Application

      │

      ▼

Temp Table (#Orders)

      │

      ▼

TempDB Database
```

---

# Types of Temp Tables

## 1. Local Temp Table

Starts with a single `#`.

Example:

```sql
CREATE TABLE #Employee
(
    EmployeeId INT,
    Name NVARCHAR(100),
    Salary DECIMAL(10,2)
);
```

Characteristics

* Visible only to the current session.
* Automatically removed when the session ends.
* Most commonly used.

---

## 2. Global Temp Table

Starts with `##`.

Example:

```sql
CREATE TABLE ##Employee
(
    EmployeeId INT,
    Name NVARCHAR(100)
);
```

Characteristics

* Accessible by all sessions.
* Deleted when the last session using it disconnects.

---

# Creating a Temp Table

### Method 1

```sql
CREATE TABLE #Employee
(
    EmployeeId INT,
    Name NVARCHAR(100),
    Salary DECIMAL(10,2)
);
```

Insert data:

```sql
INSERT INTO #Employee
VALUES
(1,'John',50000),
(2,'David',60000);
```

Retrieve:

```sql
SELECT *
FROM #Employee;
```

---

### Method 2 – SELECT INTO

```sql
SELECT *
INTO #Employee
FROM Employee;
```

SQL Server creates the table automatically.

---

# Updating Temp Tables

```sql
UPDATE #Employee
SET Salary = 70000
WHERE EmployeeId = 1;
```

---

# Deleting Rows

```sql
DELETE
FROM #Employee
WHERE EmployeeId = 2;
```

---

# Dropping Temp Tables

```sql
DROP TABLE #Employee;
```

SQL Server also drops Local Temp Tables automatically when the session ends.

---

# Indexes on Temp Tables

Temp Tables support indexes.

```sql
CREATE TABLE #Employee
(
    EmployeeId INT,
    Name NVARCHAR(100)
);

CREATE INDEX IX_Name
ON #Employee(Name);
```

Benefits:

* Faster filtering
* Faster joins
* Faster sorting

---

# Primary Keys

```sql
CREATE TABLE #Employee
(
    EmployeeId INT PRIMARY KEY,
    Name NVARCHAR(100)
);
```

---

# Constraints

Supported constraints include:

* PRIMARY KEY
* UNIQUE
* CHECK
* DEFAULT
* FOREIGN KEY (when appropriate)

---

# Joining Temp Tables

```sql
SELECT
    e.Name,
    d.DepartmentName
FROM #Employee e
JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Temp Tables behave like regular tables.

---

# Temp Tables Inside Stored Procedures

Example:

```sql
CREATE PROCEDURE GetMonthlySales
AS
BEGIN

    CREATE TABLE #Sales
    (
        OrderId INT,
        Amount MONEY
    );

    INSERT INTO #Sales
    SELECT OrderId, Amount
    FROM Orders
    WHERE MONTH(OrderDate) = MONTH(GETDATE());

    SELECT *
    FROM #Sales;

END
```

Very common in reporting procedures.

---

# Temp Table Lifecycle

```
Create Temp Table
        │
        ▼
Insert Data
        │
        ▼
Create Indexes (Optional)
        │
        ▼
Execute Multiple Queries
        │
        ▼
Drop Table
```

---

# Temp Table vs CTE

| Temp Table                         | CTE                                  |
| ---------------------------------- | ------------------------------------ |
| Stored in TempDB                   | Exists only during one SQL statement |
| Supports indexes                   | Cannot create indexes                |
| Reusable                           | Not reusable                         |
| Better for large datasets          | Better for simple queries            |
| Good for multiple processing steps | Excellent for readability            |
| Supports updates                   | Limited scope                        |

---

# Temp Table vs Table Variable

| Temp Table                | Table Variable                                               |
| ------------------------- | ------------------------------------------------------------ |
| Stored in TempDB          | Stored in TempDB                                             |
| Supports indexes          | Limited indexing support                                     |
| Has statistics            | Historically limited statistics (improved in newer versions) |
| Better for large datasets | Better for small datasets                                    |
| Supports ALTER TABLE      | Cannot ALTER after declaration                               |
| Good for complex joins    | Good for simple procedural logic                             |

---

# Performance Example

Bad

```sql
SELECT ...
FROM Orders
JOIN Customers
JOIN Products
JOIN Payments;
```

Executed five times.

SQL repeats all joins every time.

Better

```sql
SELECT *
INTO #Orders
FROM Orders
JOIN Customers
JOIN Products;
```

Later:

```sql
SELECT *
FROM #Orders
JOIN Payments;
```

The expensive join is executed only once.

---

# Real-World Example

Monthly Sales Report

```
Orders
     │
     ▼
Filter Current Month
     │
     ▼
Store In Temp Table
     │
     ▼
Join Customers
     │
     ▼
Join Products
     │
     ▼
Calculate Totals
     │
     ▼
Return Report
```

This approach reduces repeated processing.

---

# Best Practices

* Use Temp Tables for large intermediate result sets.
* Add indexes if the Temp Table is queried multiple times.
* Remove unnecessary columns.
* Drop Temp Tables when finished.
* Keep Temp Tables as small as possible.
* Avoid creating many unnecessary Temp Tables.

---

# Common Interview Questions

## What is a Temp Table?

A temporary table stored in TempDB that exists for the current session (or until explicitly dropped).

---

## Where are Temp Tables stored?

Inside the SQL Server **TempDB** database.

---

## Difference between Local and Global Temp Tables?

Local Temp Table

```sql
#Employee
```

* Session-specific
* Automatically deleted when the session ends

Global Temp Table

```sql
##Employee
```

* Shared across sessions
* Deleted after the last session using it disconnects

---

## Can Temp Tables have indexes?

Yes.

They support clustered and non-clustered indexes.

---

## Can Temp Tables have primary keys?

Yes.

---

## Why use Temp Tables?

* Reuse intermediate data
* Improve performance
* Simplify complex queries
* Support indexing
* Reduce repeated joins

---

## Temp Table vs CTE?

Use a **CTE** for:

* Readability
* Recursive queries
* One-time query logic

Use a **Temp Table** for:

* Large datasets
* Multiple query reuse
* Indexing intermediate results
* Reporting and ETL

---

## Temp Table vs Table Variable?

Choose a **Temp Table** when:

* Working with large data volumes
* Performing multiple joins
* Creating indexes
* Building reports

Choose a **Table Variable** when:

* Handling small datasets
* Using temporary data within a short batch
* Simplicity is more important than optimization

---

# Lead-Level Interview Tips

### Scenario 1

**Question:** Why did you choose a Temp Table instead of a CTE?

**Answer:**

"I needed to reuse the intermediate result multiple times, create indexes on it, and improve performance. A CTE is ideal for a single statement, whereas a Temp Table is better for multi-step processing."

---

### Scenario 2

**Question:** When would you avoid using Temp Tables?

**Answer:**

"I avoid Temp Tables for very small datasets or simple one-time queries because creating and maintaining them adds overhead. In those cases, a CTE or derived table is usually sufficient."

---

# Key Takeaways

* Temp Tables are stored in **TempDB**.
* Local Temp Tables (`#`) are session-specific.
* Global Temp Tables (`##`) are shared across sessions.
* Temp Tables support indexes, constraints, and joins.
* They are ideal for large intermediate datasets and multi-step processing.
* Use Temp Tables instead of CTEs when data needs to be reused or indexed.
* Keep Temp Tables small and drop them when no longer needed.
