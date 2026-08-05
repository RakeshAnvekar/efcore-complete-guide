# SQL Server Table Variables – Complete Interview Notes

# Overview

A **Table Variable** is a special variable in SQL Server that stores rows and columns temporarily.

It behaves like a table but is declared like a variable.

Table Variables are commonly used for:

* Small temporary datasets
* Stored Procedures
* User Defined Functions
* Intermediate calculations
* Simple joins

---

# What is a Table Variable?

Unlike a normal variable,

```sql
DECLARE @Name NVARCHAR(100)
```

a Table Variable stores multiple rows.

Example:

```sql
DECLARE @Employee TABLE
(
    EmployeeId INT,
    Name NVARCHAR(100),
    Salary DECIMAL(10,2)
);
```

---

# Why Do We Need Table Variables?

Suppose a Stored Procedure performs multiple calculations.

Instead of repeatedly querying the database,

store the intermediate data.

```text
Application

      │

      ▼

Table Variable

      │

      ▼

Multiple Queries
```

---

# Creating a Table Variable

```sql
DECLARE @Employee TABLE
(
    EmployeeId INT,
    Name NVARCHAR(100),
    Salary DECIMAL(10,2)
);
```

---

# Insert Data

```sql
INSERT INTO @Employee
VALUES
(1,'John',50000),
(2,'David',70000),
(3,'Alex',65000);
```

---

# Select Data

```sql
SELECT *
FROM @Employee;
```

---

# Update Data

```sql
UPDATE @Employee
SET Salary = 75000
WHERE EmployeeId = 2;
```

---

# Delete Data

```sql
DELETE
FROM @Employee
WHERE EmployeeId = 3;
```

---

# Join with Another Table

```sql
SELECT
    e.Name,
    d.DepartmentName
FROM @Employee e
JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Table Variables support JOIN operations just like normal tables.

---

# Primary Key

```sql
DECLARE @Employee TABLE
(
    EmployeeId INT PRIMARY KEY,
    Name NVARCHAR(100)
);
```

---

# Unique Constraint

```sql
DECLARE @Employee TABLE
(
    EmployeeId INT,
    Email NVARCHAR(100) UNIQUE
);
```

---

# Check Constraint

```sql
DECLARE @Employee TABLE
(
    Salary MONEY
    CHECK (Salary > 0)
);
```

---

# Default Constraint

```sql
DECLARE @Employee TABLE
(
    CreatedDate DATETIME
    DEFAULT GETDATE()
);
```

---

# Scope of a Table Variable

A Table Variable exists only inside the current batch, stored procedure, or function.

```text
Stored Procedure

        │

Declare Table Variable

        │

Use Data

        │

Procedure Ends

        │

Destroyed
```

It cannot be accessed from another session.

---

# Where Is a Table Variable Stored?

Many candidates answer:

```text
Memory
```

This is **not completely correct**.

Actually,

Table Variables are stored in:

```text
TempDB
```

Small datasets may stay cached in memory, but SQL Server manages them through TempDB.

---

# Statistics

Historically (before SQL Server 2019):

Table Variables had **very limited statistics**.

SQL Server often assumed:

```text
1 Row
```

This could lead to poor execution plans for large datasets.

### SQL Server 2019+

Deferred Compilation improves row estimation by compiling the query after the Table Variable has been populated.

---

# Indexes

You cannot create an index separately like this:

```sql
CREATE INDEX IX_Name
ON @Employee(Name);
```

Instead, indexes are created indirectly using constraints.

Example:

```sql
DECLARE @Employee TABLE
(
    EmployeeId INT PRIMARY KEY,
    Email NVARCHAR(100) UNIQUE
);
```

These constraints automatically create indexes.

---

# Transactions

Table Variables participate in transactions.

```sql
BEGIN TRANSACTION;

DECLARE @Orders TABLE
(
    OrderId INT
);

INSERT INTO @Orders
VALUES (1);

ROLLBACK;
```

The transaction behavior is the same as other SQL operations.

---

# Table Variable Lifecycle

```text
Declare Table Variable
        │
        ▼
Insert Data
        │
        ▼
Read / Update / Delete
        │
        ▼
Batch Ends
        │
        ▼
Automatically Removed
```

---

# Performance

Excellent for:

* Small datasets
* Temporary calculations
* Small lookup tables
* Functions
* Short stored procedures

Poor choice for:

* Hundreds of thousands of rows
* Complex reporting
* Heavy joins
* Large aggregations

---

# Table Variable vs Temp Table

| Table Variable                                                 | Temp Table                                   |
| -------------------------------------------------------------- | -------------------------------------------- |
| Declared using `DECLARE`                                       | Created using `CREATE TABLE`                 |
| Stored in TempDB                                               | Stored in TempDB                             |
| Scope is current batch/procedure                               | Scope is current session                     |
| Supports PRIMARY KEY and UNIQUE indexes through constraints    | Supports clustered and non-clustered indexes |
| Cannot ALTER after declaration                                 | Can ALTER after creation                     |
| Better for small datasets                                      | Better for large datasets                    |
| Historically limited statistics (improved in SQL Server 2019+) | Full statistics support                      |
| Good for procedural logic                                      | Good for reporting and complex processing    |

---

# Table Variable vs CTE

| Table Variable                  | CTE                               |
| ------------------------------- | --------------------------------- |
| Stores data                     | Does not store data permanently   |
| Can be reused                   | Valid for one statement           |
| Good for multiple operations    | Good for readability              |
| Supports INSERT, UPDATE, DELETE | Mainly used for query expressions |
| Better for temporary storage    | Better for recursive queries      |

---

# Table Variable vs Derived Table

| Table Variable                 | Derived Table                      |
| ------------------------------ | ---------------------------------- |
| Can be reused                  | Exists only inside one query       |
| Stores rows temporarily        | Inline query only                  |
| Better for multiple operations | Better for simple one-time queries |

---

# Real-World Example

Suppose a Stored Procedure calculates monthly bonuses.

```text
Employee

      │

Calculate Bonus

      │

Store In Table Variable

      │

Update Payroll

      │

Return Result
```

The intermediate bonus calculations can be stored in a Table Variable.

---

# Best Practices

* Use Table Variables for small datasets.
* Avoid using them for large reports.
* Use PRIMARY KEY or UNIQUE constraints when appropriate.
* Prefer Temp Tables when multiple indexes or statistics are needed.
* Keep the number of rows small.

---

# Common Interview Questions

## What is a Table Variable?

A temporary table-like structure declared using `DECLARE` that stores rows for the duration of the current batch, stored procedure, or function.

---

## Where is a Table Variable stored?

It is managed through **TempDB**.

---

## Can Table Variables have indexes?

Yes, through:

* PRIMARY KEY
* UNIQUE constraints

You cannot create separate indexes using `CREATE INDEX`.

---

## Can Table Variables be altered?

No.

Once declared, their schema cannot be changed.

---

## Can Table Variables participate in transactions?

Yes.

---

## Table Variable vs Temp Table?

Use a **Table Variable** for:

* Small datasets
* Stored procedures
* Functions
* Temporary calculations

Use a **Temp Table** for:

* Large datasets
* Reporting
* Multiple joins
* Performance-critical queries
* When additional indexes or statistics are needed

---

## Why can Table Variables perform poorly?

Historically, SQL Server estimated only one row for Table Variables, leading to inefficient execution plans for large datasets. SQL Server 2019 introduced deferred compilation to improve this behavior.

---

# Lead-Level Interview Tips

### Scenario 1

**Question:** Why didn't you use a Temp Table?

**Answer:**

"The dataset was very small and only needed within a single stored procedure. A Table Variable was simpler and sufficient for the workload."

---

### Scenario 2

**Question:** When would you replace a Table Variable with a Temp Table?

**Answer:**

"If the dataset grows significantly, requires multiple joins, or benefits from additional indexes and statistics, I would replace the Table Variable with a Temp Table to improve execution plans and overall performance."

---

# Key Takeaways

* Table Variables are declared using `DECLARE`.
* They are managed through **TempDB**.
* Best suited for **small datasets**.
* They support **PRIMARY KEY**, **UNIQUE**, **CHECK**, and **DEFAULT** constraints.
* Their scope is limited to the current batch, stored procedure, or function.
* They cannot be altered after declaration.
* For larger datasets or complex processing, Temp Tables are generally the better choice.
