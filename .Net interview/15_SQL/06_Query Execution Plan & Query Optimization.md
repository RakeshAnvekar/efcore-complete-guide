# SQL Query Execution Plan & Query Optimization – Interview Notes

## Overview

When you execute a SQL query, SQL Server does **not** directly read the data.

Instead, SQL Server first determines the most efficient way to execute the query by creating an **Execution Plan**.

Understanding execution plans is one of the most important skills for SQL performance tuning.

---

# How SQL Server Executes a Query

```
SQL Query
    │
    ▼
Parser
    │
    ▼
Query Optimizer
    │
    ▼
Execution Plan
    │
    ▼
Execute Query
```

### Step 1 – Parser

Checks the SQL syntax.

Example:

```sql
SELECT *
FROM Orders;
```

If the syntax is invalid, SQL Server returns an error.

---

### Step 2 – Query Optimizer

The Query Optimizer evaluates different execution strategies and selects the one with the lowest estimated cost.

It considers:

* Available indexes
* Table statistics
* Number of rows
* CPU cost
* Memory usage
* Disk I/O
* Join algorithms

---

### Step 3 – Execution Plan

The optimizer creates a roadmap describing how SQL Server will retrieve the requested data.

---

# Example

```sql
SELECT *
FROM Orders
WHERE CustomerId = 101;
```

If there is no index:

```
Orders

Row1
 ↓
Row2
 ↓
Row3
 ↓
...
 ↓
Row1000000
```

Every row is checked.

This is called a **Table Scan**.

---

If an index exists:

```
CustomerId Index

100 → Row2500
101 → Row5000
102 → Row8000
```

SQL directly jumps to Row5000.

This is called an **Index Seek**.

---

# Table Scan

Definition:

Reads every row in the table.

Advantages:

* Efficient for very small tables.
* Useful when most rows are required.

Disadvantages:

* Very slow on large tables.
* High Disk I/O.
* High CPU usage.

Example:

```sql
SELECT *
FROM Orders
WHERE CustomerName='John';
```

If CustomerName is not indexed, SQL performs a Table Scan.

---

# Index Seek

Definition:

Uses an index to directly locate the required rows.

Advantages:

* Very fast.
* Minimal I/O.
* Minimal CPU usage.

Example:

```sql
CREATE INDEX IX_Orders_CustomerId
ON Orders(CustomerId);
```

Query:

```sql
SELECT *
FROM Orders
WHERE CustomerId=100;
```

Execution:

```
Index

↓

Locate CustomerId

↓

Return Matching Rows
```

---

# Index Scan

Reads every entry in the index.

Usually better than a Table Scan because indexes are smaller.

---

# Clustered Index Scan

Reads the entire clustered index.

Since clustered indexes contain the table data, this is similar to scanning the table.

---

# Key Lookup

Suppose the following index exists:

```sql
CREATE INDEX IX_Department
ON Employee(Department);
```

Query:

```sql
SELECT Name, Salary
FROM Employee
WHERE Department='IT';
```

The index contains only Department.

SQL performs:

```
Index Seek

↓

Find Matching Row

↓

Go Back To Table

↓

Read Name

↓

Read Salary
```

This additional table access is called a **Key Lookup**.

---

# Covering Index

A Covering Index contains all columns required by the query.

Example:

```sql
CREATE INDEX IX_Department
ON Employee(Department)
INCLUDE(Name, Salary);
```

Now SQL can answer the query using only the index.

Benefits:

* Eliminates Key Lookup.
* Faster execution.
* Lower I/O.

---

# Common Execution Plan Operators

| Operator             | Description                              |
| -------------------- | ---------------------------------------- |
| Table Scan           | Reads every row in the table             |
| Index Seek           | Reads only required rows                 |
| Index Scan           | Reads all index entries                  |
| Clustered Index Scan | Reads entire clustered index             |
| Key Lookup           | Retrieves missing columns from the table |
| Sort                 | Sorts rows                               |
| Hash Match           | Used for joins and aggregations          |
| Nested Loop          | Efficient join for smaller datasets      |
| Merge Join           | Efficient when inputs are sorted         |

---

# Cost-Based Optimization

The optimizer estimates the execution cost based on:

* CPU
* Memory
* Disk I/O
* Network
* Estimated row count

The plan with the lowest estimated cost is selected.

---

# Why SELECT * Is Bad

Bad:

```sql
SELECT *
FROM Orders;
```

Problems:

* Reads unnecessary columns.
* More network traffic.
* Higher memory usage.
* Often prevents Covering Index usage.

Good:

```sql
SELECT OrderId,
       CustomerId,
       TotalAmount
FROM Orders;
```

Always retrieve only the required columns.

---

# SARGable Queries

SARGable = **Search ARGument Able**

A SARGable query allows SQL Server to efficiently use indexes.

## Bad Example

```sql
SELECT *
FROM Employee
WHERE YEAR(HireDate)=2025;
```

Problem:

The function is applied to the indexed column.

Result:

Table Scan or Index Scan.

---

## Good Example

```sql
SELECT *
FROM Employee
WHERE HireDate >= '2025-01-01'
AND HireDate < '2026-01-01';
```

Result:

Index Seek.

---

Another Bad Example

```sql
WHERE UPPER(Name)='RAKESH'
```

Better

```sql
WHERE Name='Rakesh'
```

(Assuming a case-insensitive collation.)

---

# Parameter Sniffing

Example:

```sql
CREATE PROCEDURE GetOrders
    @CustomerId INT
AS
SELECT *
FROM Orders
WHERE CustomerId=@CustomerId;
```

First Execution:

```
CustomerId = 1

100000 Rows
```

SQL creates a plan.

Later:

```
CustomerId = 9000

2 Rows
```

SQL may reuse the same execution plan even though another plan would perform better.

This issue is called **Parameter Sniffing**.

Possible Solutions:

* OPTION (RECOMPILE)
* OPTIMIZE FOR
* Query tuning
* Updating statistics
* Assigning parameter to local variables (in specific scenarios)

---

# Reading an Execution Plan

Good Plan

```
Index Seek

↓

Key Lookup

↓

Return Results
```

Bad Plan

```
Table Scan

↓

Sort

↓

Hash Match

↓

Return Results
```

---

# Steps to Optimize a Slow Query

1. View the Actual Execution Plan.
2. Look for Table Scans or Index Scans on large tables.
3. Verify indexes on WHERE and JOIN columns.
4. Avoid SELECT *.
5. Make predicates SARGable.
6. Reduce expensive Sort and Key Lookup operations.
7. Update statistics when necessary.
8. Investigate Parameter Sniffing.

---

# Best Practices

* Create indexes on frequently searched columns.
* Index JOIN columns.
* Index foreign keys.
* Retrieve only required columns.
* Avoid functions on indexed columns.
* Review execution plans for slow queries.
* Keep statistics updated.
* Remove unused indexes.
* Use Covering Indexes where appropriate.

---

# Interview Questions

### What is an Execution Plan?

An Execution Plan is the strategy SQL Server uses to execute a query efficiently.

---

### What does the Query Optimizer do?

It evaluates multiple execution strategies and chooses the one with the lowest estimated cost.

---

### What is the difference between Index Seek and Table Scan?

**Index Seek**

* Reads only required rows.
* Fast.
* Uses indexes.

**Table Scan**

* Reads every row.
* Slower for large tables.
* Used when no suitable index exists or a scan is cheaper.

---

### What is a Key Lookup?

A Key Lookup occurs when SQL Server uses an index to locate rows but must access the base table to retrieve additional columns.

---

### What is a Covering Index?

A Covering Index contains every column needed by a query, eliminating the need for Key Lookups.

---

### What is a SARGable Query?

A query written so SQL Server can efficiently use indexes, typically by avoiding functions or calculations on indexed columns.

---

### What is Parameter Sniffing?

A situation where SQL Server reuses an execution plan generated for one parameter value, which may be inefficient for different parameter values.

---

# Interview Summary

```
Slow Query
     │
     ▼
Execution Plan
     │
     ▼
Table Scan?
     │
     ├── Yes → Check Missing Index
     │
     └── No
           │
           ▼
Index Scan?
           │
           ▼
Key Lookup?
           │
           ▼
SARGable Query?
           │
           ▼
Statistics Updated?
           │
           ▼
Parameter Sniffing?
           │
           ▼
Optimize Query
```

## Key Takeaways

* Always analyze the Execution Plan before tuning a query.
* Prefer **Index Seek** over **Table Scan** whenever appropriate.
* Avoid `SELECT *` in production code.
* Write **SARGable** predicates.
* Use **Covering Indexes** to eliminate Key Lookups.
* Keep statistics current.
* Understand **Parameter Sniffing** and when it affects performance.
* Optimize based on evidence from the execution plan, not assumptions.
