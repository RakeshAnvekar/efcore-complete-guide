# 🚀 SQL JOINS – Complete Interview Guide

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Senior .NET Developer
>
> **Key Concept:** SQL JOIN is used to retrieve data from two or more tables based on a related column.

---

# 📚 Table of Contents

- What is JOIN?
- Why Do We Need JOIN?
- Sample Tables
- INNER JOIN
- LEFT JOIN
- RIGHT JOIN
- FULL OUTER JOIN
- CROSS JOIN
- SELF JOIN
- JOIN Comparison
- Real-Time Examples
- Common Interview Questions
- Summary
- Interview One-Liner

---

# 🎯 What is JOIN?

A JOIN combines rows from two or more tables based on a related column.

Example

Employee Table

| EmployeeId | Name | DepartmentId |
|------------|------|--------------|
| 1 | John | 1 |
| 2 | David | 2 |
| 3 | Smith | 3 |
| 4 | Alex | NULL |

Department Table

| DepartmentId | DepartmentName |
|--------------|----------------|
| 1 | HR |
| 2 | IT |
| 4 | Finance |

Relationship

Employee.DepartmentId

↓

Department.DepartmentId

---

# Why Do We Need JOIN?

Suppose we need

Employee Name

Department Name

The Employee table doesn't contain the department name.

The Department table doesn't contain employee names.

JOIN combines them.

---

# INNER JOIN ⭐⭐⭐⭐⭐

Returns only matching records from both tables.

Syntax

```sql
SELECT e.Name,
       d.DepartmentName
FROM Employee e
INNER JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Output

| Name | Department |
|------|------------|
| John | HR |
| David | IT |

Explanation

Smith is excluded because DepartmentId = 3 doesn't exist.

Alex is excluded because DepartmentId is NULL.

Visualization

Employee

```
John

David

Smith

Alex
```

Department

```
HR

IT

Finance
```

INNER JOIN

```
John → HR

David → IT
```

Only matching records.

---

# LEFT JOIN ⭐⭐⭐⭐⭐

Returns

All rows from the LEFT table

+

Matching rows from RIGHT table.

Syntax

```sql
SELECT e.Name,
       d.DepartmentName
FROM Employee e
LEFT JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Output

| Name | Department |
|------|------------|
| John | HR |
| David | IT |
| Smith | NULL |
| Alex | NULL |

Visualization

LEFT TABLE

```
John

David

Smith

Alex
```

Always returned.

If department doesn't exist

↓

NULL

---

# RIGHT JOIN ⭐⭐⭐⭐

Returns

All rows from RIGHT table

+

Matching rows from LEFT table.

```sql
SELECT e.Name,
       d.DepartmentName
FROM Employee e
RIGHT JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Output

| Name | Department |
|------|------------|
| John | HR |
| David | IT |
| NULL | Finance |

Finance has no employee.

Still returned.

---

# FULL OUTER JOIN ⭐⭐⭐⭐

Returns

Everything

Left

+

Right

Matching or Not.

```sql
SELECT e.Name,
       d.DepartmentName
FROM Employee e
FULL OUTER JOIN Department d
ON e.DepartmentId = d.DepartmentId;
```

Output

| Name | Department |
|------|------------|
| John | HR |
| David | IT |
| Smith | NULL |
| Alex | NULL |
| NULL | Finance |

---

# CROSS JOIN ⭐⭐⭐

Returns

Every Employee

×

Every Department

```sql
SELECT *

FROM Employee

CROSS JOIN Department;
```

Suppose

Employee

```
3 Rows
```

Department

```
4 Rows
```

Output

```
3 × 4

=

12 Rows
```

Every employee is paired with every department.

---

# SELF JOIN ⭐⭐⭐⭐

A table joins with itself.

Example

Employee

| Id | Name | ManagerId |
|----|------|-----------|
|1|John|NULL|
|2|David|1|
|3|Smith|1|

Query

```sql
SELECT

e.Name AS Employee,

m.Name AS Manager

FROM Employee e

LEFT JOIN Employee m

ON e.ManagerId = m.Id;
```

Output

| Employee | Manager |
|-----------|----------|
| John | NULL |
| David | John |
| Smith | John |

Used for

Employee → Manager

Category → Parent Category

Folder → Parent Folder

---

# JOIN Comparison

| JOIN | Returns |
|------|----------|
| INNER JOIN | Only Matching Records |
| LEFT JOIN | All Left + Matching Right |
| RIGHT JOIN | All Right + Matching Left |
| FULL JOIN | Everything |
| CROSS JOIN | Cartesian Product |
| SELF JOIN | Table Joins Itself |

---

# Visual Representation

INNER JOIN

```
Employee

(●)

Department

(●)

Only Intersection
```

LEFT JOIN

```
Entire Employee

+

Matching Department
```

RIGHT JOIN

```
Entire Department

+

Matching Employee
```

FULL JOIN

```
Employee

+

Department

Everything
```

---

# Real-Time Examples

## Employee & Department

```
Employee

↓

Department
```

---

## Customer & Orders

```
Customer

↓

Orders
```

---

## Student & Course

```
Student

↓

Course
```

---

## Employee & Manager

```
Employee

↓

Employee

(Self Join)
```

---

# Common Interview Questions

## Difference between INNER JOIN and LEFT JOIN?

INNER JOIN returns only matching records.

LEFT JOIN returns all rows from the left table, even if there is no match.

---

## Which JOIN returns unmatched rows?

LEFT JOIN

RIGHT JOIN

FULL JOIN

---

## Which JOIN returns all possible combinations?

CROSS JOIN.

---

## Why use SELF JOIN?

To retrieve hierarchical relationships like Employee → Manager.

---

## Which JOIN is most used?

INNER JOIN

LEFT JOIN

---

# Summary

| Requirement | JOIN |
|------------|------|
| Matching Records Only | INNER JOIN |
| All Employees | LEFT JOIN |
| All Departments | RIGHT JOIN |
| Everything | FULL JOIN |
| Every Combination | CROSS JOIN |
| Employee & Manager | SELF JOIN |

---

# Interview One-Liner

> **SQL JOIN combines data from multiple tables using a related column. INNER JOIN returns matching rows, LEFT JOIN returns all rows from the left table, RIGHT JOIN returns all rows from the right table, FULL JOIN returns all rows from both tables, CROSS JOIN returns every possible combination, and SELF JOIN joins a table with itself.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

**"Which JOIN do you use most in real projects?"**

Answer:

- **INNER JOIN** – When related data must exist in both tables (e.g., Employee with Department).
- **LEFT JOIN** – Most common in enterprise applications because it returns all records from the primary table even if related data is missing (e.g., show all employees even if some are not assigned to a department).
- **SELF JOIN** – Used for hierarchical data like Employee → Manager.