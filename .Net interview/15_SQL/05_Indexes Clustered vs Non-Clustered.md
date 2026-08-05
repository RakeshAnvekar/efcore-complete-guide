# 🚀 SQL Module 6 – Indexes (Clustered vs Non-Clustered)

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Mid → Senior .NET Developer
>
> **Key Concept:** An Index improves the speed of data retrieval by creating a fast lookup structure, similar to the index of a book.

---

# 📚 Table of Contents

- What is an Index?
- Why Do We Need an Index?
- How Does an Index Work?
- Clustered Index
- Non-Clustered Index
- Clustered vs Non-Clustered Index
- Composite Index
- Unique Index
- Covering Index
- When SQL Uses an Index
- When SQL Doesn't Use an Index
- Advantages
- Disadvantages
- Real-Time Examples
- Common Interview Questions
- Summary
- Interview One-Liner

---

# 🎯 What is an Index?

An Index is a database object that helps SQL Server find rows faster.

Think of it like the index of a book.

Without Index

```
Book

↓

Page 1

↓

Page 2

↓

Page 3

↓

Page 500
```

Search is slow.

---

With Index

```
Index

↓

Chapter

↓

Page Number

↓

Directly Go To Page
```

Much faster.

---

# Real-Life Example

Imagine a phone book.

Without Index

```
Start Reading

↓

A

↓

B

↓

C

↓

...

↓

Z
```

With Index

```
Search

↓

"John"

↓

Jump Directly
```

---

# Sample Employee Table

| Id | Name | Department | Salary |
|----|------|------------|--------|
|1|John|HR|50000|
|2|David|IT|70000|
|3|Alex|HR|45000|
|4|Smith|Finance|80000|

---

# Why Do We Need an Index?

Without Index

```sql
SELECT *

FROM Employee

WHERE Id=4;
```

SQL scans every row.

```
Row 1

↓

Row 2

↓

Row 3

↓

Row 4

↓

Found
```

This is called

```
Table Scan
```

---

With Index

```
Index

↓

Id=4

↓

Jump Directly

↓

Return Row
```

This is called

```
Index Seek
```

Much faster.

---

# Clustered Index ⭐⭐⭐⭐⭐

A Clustered Index stores the table data physically in sorted order.

Think of the table itself being sorted.

```
Clustered Index

↓

Table Data Sorted
```

Example

```
Employee Table

1

2

3

4

5

6
```

Rows are physically stored in this order.

---

Create Clustered Index

```sql
CREATE CLUSTERED INDEX IX_Employee_Id

ON Employee(Id);
```

---

Important Points

✔ Only ONE Clustered Index per table

✔ Usually created on Primary Key

✔ Data is physically sorted

---

# Non-Clustered Index ⭐⭐⭐⭐⭐

A Non-Clustered Index is stored separately from the table.

It contains

```
Indexed Value

↓

Pointer

↓

Actual Row
```

Example

```
Index

David

↓

Pointer

↓

Employee Row
```

Table remains unchanged.

---

Create Non-Clustered Index

```sql
CREATE NONCLUSTERED INDEX IX_Employee_Name

ON Employee(Name);
```

---

Important Points

✔ Multiple Non-Clustered Indexes

✔ Data is NOT physically sorted

✔ Stores pointers to actual rows

---

# Clustered vs Non-Clustered

| Feature | Clustered | Non-Clustered |
|----------|-----------|---------------|
| Physical Sorting | Yes | No |
| Number Allowed | 1 | Many |
| Stores Data | Yes | No |
| Pointer Required | No | Yes |
| Faster Range Search | Yes | Good for Searches |

---

# Composite Index

Index on multiple columns.

```sql
CREATE INDEX IX_Employee

ON Employee

(

Department,

Salary

);
```

Useful when searching by both columns.

---

# Unique Index

Prevents duplicate values.

```sql
CREATE UNIQUE INDEX IX_Email

ON Employee(Email);
```

---

# Covering Index

Suppose query

```sql
SELECT Name,

Salary

FROM Employee

WHERE Department='IT';
```

Create

```sql
CREATE INDEX IX_Dept

ON Employee(Department)

INCLUDE(Name,Salary);
```

SQL reads only the index.

No table lookup.

Very fast.

---

# When SQL Uses Index

```sql
SELECT *

FROM Employee

WHERE Id=10;
```

Uses Index Seek.

---

# When SQL Ignores Index

Example

```sql
SELECT *

FROM Employee

WHERE YEAR(HireDate)=2024;
```

Function prevents index usage.

Better

```sql
WHERE HireDate

BETWEEN

'2024-01-01'

AND

'2024-12-31'
```

---

Another Example

Bad

```sql
WHERE Name LIKE '%John%'
```

Good

```sql
WHERE Name LIKE 'John%'
```

---

# Table Scan vs Index Seek

Without Index

```
Employee

↓

Read Row 1

↓

Read Row 2

↓

Read Row 3

↓

Read Row 1000000
```

---

With Index

```
Employee Index

↓

Jump Directly

↓

Return Row
```

---

# Advantages

✔ Faster SELECT

✔ Faster JOIN

✔ Faster ORDER BY

✔ Faster GROUP BY

✔ Better Performance

---

# Disadvantages

❌ Slower INSERT

❌ Slower UPDATE

❌ Slower DELETE

❌ Extra Disk Space

Reason

Every index must also be updated when data changes.

---

# Real-Time Example

Employee Search

```sql
SELECT *

FROM Employee

WHERE EmployeeId=50000;
```

Without Index

```
Scan 10 Million Rows
```

With Index

```
Jump Directly

↓

Employee Found
```

---

# Common Interview Questions

## What is an Index?

A database object that improves query performance by allowing faster data retrieval.

---

## Why is an Index faster?

Because SQL Server performs an Index Seek instead of scanning the entire table.

---

## Difference between Clustered and Non-Clustered Index?

| Clustered | Non-Clustered |
|------------|---------------|
| Data Physically Sorted | Separate Structure |
| One Per Table | Multiple Allowed |

---

## Why only one Clustered Index?

Because table rows can be physically sorted in only one order.

---

## Can a table exist without a Clustered Index?

Yes.

Such a table is called a **Heap**.

---

## Why are too many indexes bad?

Because INSERT, UPDATE, and DELETE operations become slower as every index must also be maintained.

---

## What is a Covering Index?

An index that contains all the columns required by a query, allowing SQL Server to return results without accessing the base table.

---

# 📌 Summary

| Requirement | Recommended |
|-------------|-------------|
| Fast Primary Key Search | Clustered Index |
| Search by Name | Non-Clustered Index |
| Search by Multiple Columns | Composite Index |
| Prevent Duplicate Values | Unique Index |
| Improve Read Performance | Index |
| Improve Write Performance | Fewer Indexes |

---

# 🎯 Interview One-Liner

> **An Index is a database object that improves query performance by reducing the number of rows SQL Server must scan. A Clustered Index stores data physically in sorted order and only one is allowed per table. A Non-Clustered Index stores indexed values with pointers to the actual rows, allowing multiple indexes on a table.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

## "How do you improve a slow SQL query?"

Answer:

1. Check the execution plan.
2. Look for **Table Scans**.
3. Create appropriate **Clustered** or **Non-Clustered Indexes**.
4. Avoid functions on indexed columns.
5. Avoid `SELECT *`; retrieve only required columns.
6. Use **Covering Indexes** for frequently executed queries.
7. Ensure joins and WHERE clauses use indexed columns.

These are the practical steps commonly used to optimize SQL Server performance in enterprise applications.