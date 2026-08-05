# 🚀 SQL Module 2 – GROUP BY & HAVING

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Beginner → Senior .NET Developer
>
> **Key Concept:** `GROUP BY` groups rows that have the same values into summary rows. It is commonly used with aggregate functions such as `COUNT()`, `SUM()`, `AVG()`, `MIN()`, and `MAX()`. `HAVING` filters the grouped data.

---

# 📚 Table of Contents

- What is GROUP BY?
- Why Do We Need GROUP BY?
- Sample Table
- COUNT()
- SUM()
- AVG()
- MIN()
- MAX()
- GROUP BY Multiple Columns
- WHERE + GROUP BY
- HAVING
- WHERE vs HAVING
- SQL Execution Order
- Real-Time Examples
- Common Interview Questions
- Summary
- Interview One-Liner

---

# Sample Employee Table

| EmployeeId | Name | Department | City | Salary |
|------------|------|------------|------|--------|
|1|John|HR|Bangalore|50000|
|2|David|IT|Mumbai|60000|
|3|Alex|HR|Bangalore|45000|
|4|Smith|IT|Mumbai|70000|
|5|Bob|HR|Delhi|55000|
|6|Kevin|Sales|Delhi|65000|

---

# 🎯 What is GROUP BY?

`GROUP BY` combines rows having the same value into one group.

Example

```
HR

↓

John

Alex

Bob

↓

One Group
```

```
IT

↓

David

Smith

↓

One Group
```

---

# Why Do We Need GROUP BY?

Suppose the interviewer asks

> **How many employees are working in each department?**

Without GROUP BY

SQL cannot summarize data.

GROUP BY makes it possible.

---

# COUNT()

Count employees department-wise.

```sql
SELECT Department,
       COUNT(*) AS TotalEmployees
FROM Employee
GROUP BY Department;
```

Output

| Department | TotalEmployees |
|------------|---------------|
| HR | 3 |
| IT | 2 |
| Sales | 1 |

Execution

```
Employee Table

↓

Group by Department

↓

HR

↓

Count = 3

----------------

IT

↓

Count = 2

----------------

Sales

↓

Count = 1
```

---

# SUM()

Find total salary department-wise.

```sql
SELECT Department,
       SUM(Salary) AS TotalSalary
FROM Employee
GROUP BY Department;
```

Output

| Department | TotalSalary |
|------------|------------|
| HR | 150000 |
| IT | 130000 |
| Sales | 65000 |

---

# AVG()

Find average salary.

```sql
SELECT Department,
       AVG(Salary) AS AverageSalary
FROM Employee
GROUP BY Department;
```

Output

| Department | AverageSalary |
|------------|--------------|
| HR | 50000 |
| IT | 65000 |
| Sales | 65000 |

---

# MAX()

Find highest salary in every department.

```sql
SELECT Department,
       MAX(Salary) AS HighestSalary
FROM Employee
GROUP BY Department;
```

Output

| Department | HighestSalary |
|------------|---------------|
| HR | 55000 |
| IT | 70000 |
| Sales | 65000 |

---

# MIN()

Find lowest salary department-wise.

```sql
SELECT Department,
       MIN(Salary) AS LowestSalary
FROM Employee
GROUP BY Department;
```

Output

| Department | LowestSalary |
|------------|--------------|
| HR | 45000 |
| IT | 60000 |
| Sales | 65000 |

---

# GROUP BY Multiple Columns

Group by Department and City.

```sql
SELECT Department,
       City,
       COUNT(*) AS Employees
FROM Employee
GROUP BY Department, City;
```

Output

| Department | City | Employees |
|------------|------|----------|
| HR | Bangalore | 2 |
| HR | Delhi | 1 |
| IT | Mumbai | 2 |
| Sales | Delhi | 1 |

Execution

```
Department

↓

City

↓

Count
```

---

# WHERE + GROUP BY

Find employees earning more than 50,000.

Then group by department.

```sql
SELECT Department,
       COUNT(*) AS Employees
FROM Employee
WHERE Salary > 50000
GROUP BY Department;
```

Execution

```
Employee Table

↓

WHERE Salary > 50000

↓

Remaining Employees

↓

GROUP BY Department

↓

COUNT
```

---

# HAVING

HAVING filters groups.

Question

Find departments having more than 2 employees.

```sql
SELECT Department,
       COUNT(*) AS Employees
FROM Employee
GROUP BY Department
HAVING COUNT(*) > 2;
```

Output

| Department | Employees |
|------------|-----------|
| HR | 3 |

Execution

```
Employee Table

↓

GROUP BY Department

↓

HR

↓

Count = 3

↓

HAVING Count > 2

↓

Return HR
```

---

# HAVING with SUM()

Find departments whose total salary exceeds 120000.

```sql
SELECT Department,
       SUM(Salary) AS TotalSalary
FROM Employee
GROUP BY Department
HAVING SUM(Salary) > 120000;
```

Output

| Department | TotalSalary |
|------------|------------|
| HR |150000|
| IT |130000|

---

# HAVING with AVG()

Departments where average salary is above 55000.

```sql
SELECT Department,
       AVG(Salary) AS AverageSalary
FROM Employee
GROUP BY Department
HAVING AVG(Salary) > 55000;
```

Output

| Department | AverageSalary |
|------------|---------------|
| IT |65000|
| Sales |65000|

---

# HAVING with MAX()

```sql
SELECT Department,
       MAX(Salary)
FROM Employee
GROUP BY Department
HAVING MAX(Salary) > 60000;
```

---

# HAVING with MIN()

```sql
SELECT Department,
       MIN(Salary)
FROM Employee
GROUP BY Department
HAVING MIN(Salary) < 50000;
```

---

# GROUP BY with ORDER BY

```sql
SELECT Department,
       COUNT(*) AS Employees
FROM Employee
GROUP BY Department
ORDER BY Employees DESC;
```

Output

```
HR

IT

Sales
```

Highest employee count first.

---

# GROUP BY with DISTINCT

Usually unnecessary.

Example

```sql
SELECT DISTINCT Department
FROM Employee;
```

Same Result

```sql
SELECT Department
FROM Employee
GROUP BY Department;
```

---

# WHERE vs HAVING ⭐⭐⭐⭐⭐

## WHERE

Filters rows.

```sql
SELECT *

FROM Employee

WHERE Salary > 50000;
```

Execution

```
Employee Table

↓

Remove Rows

↓

Remaining Rows
```

---

## HAVING

Filters grouped results.

```sql
SELECT Department,

COUNT(*)

FROM Employee

GROUP BY Department

HAVING COUNT(*) > 2;
```

Execution

```
Employee Table

↓

GROUP BY

↓

Department Groups

↓

HAVING

↓

Return Matching Groups
```

---

# WHERE vs HAVING Comparison

| WHERE | HAVING |
|--------|---------|
| Filters Rows | Filters Groups |
| Executes Before GROUP BY | Executes After GROUP BY |
| Cannot use Aggregate Functions directly | Can use Aggregate Functions |
| Faster | Slightly Slower |

---

# SQL Execution Order ⭐⭐⭐⭐⭐

Most Interviewers Ask This.

```
FROM

↓

WHERE

↓

GROUP BY

↓

HAVING

↓

SELECT

↓

ORDER BY
```

Remember this order.

---

# Real-Time Example 1

Employee Report

Requirement

Find department-wise employee count.

```sql
SELECT Department,
       COUNT(*)
FROM Employee
GROUP BY Department;
```

---

# Real-Time Example 2

Payroll Report

Requirement

Department-wise salary.

```sql
SELECT Department,
       SUM(Salary)
FROM Employee
GROUP BY Department;
```

---

# Real-Time Example 3

HR Dashboard

Requirement

Average salary department-wise.

```sql
SELECT Department,
       AVG(Salary)
FROM Employee
GROUP BY Department;
```

---

# Real-Time Example 4

Company Analytics

Find departments with more than two employees.

```sql
SELECT Department,
       COUNT(*)
FROM Employee
GROUP BY Department
HAVING COUNT(*) > 2;
```

---

# Common Interview Questions

## What is GROUP BY?

Groups rows having the same value.

---

## Why do we use GROUP BY?

To perform aggregate calculations on groups of rows.

---

## Can GROUP BY be used without aggregate functions?

Yes.

```sql
SELECT Department
FROM Employee
GROUP BY Department;
```

Returns unique departments.

---

## What is HAVING?

Filters grouped data after GROUP BY.

---

## Difference between WHERE and HAVING?

WHERE filters rows.

HAVING filters groups.

---

## Can HAVING be used without GROUP BY?

Yes, but it's uncommon. Without `GROUP BY`, HAVING applies to the entire result set as a single group.

---

## Which executes first?

```
FROM

↓

WHERE

↓

GROUP BY

↓

HAVING

↓

SELECT

↓

ORDER BY
```

---

# 📌 Summary

| Requirement | SQL Statement |
|-------------|---------------|
| Count Employees | COUNT() |
| Total Salary | SUM() |
| Average Salary | AVG() |
| Highest Salary | MAX() |
| Lowest Salary | MIN() |
| Group Records | GROUP BY |
| Filter Rows | WHERE |
| Filter Groups | HAVING |
| Sort Result | ORDER BY |

---

# 🎯 Interview One-Liner

> **GROUP BY groups rows based on one or more columns and is used with aggregate functions like COUNT, SUM, AVG, MIN, and MAX. WHERE filters rows before grouping, whereas HAVING filters groups after aggregation.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

## "Can you explain GROUP BY with a real project?"

Example:

In an **Employee Management System**, HR wants to know:

- Total employees in each department
- Total salary paid by department
- Average salary by department
- Departments with more than 10 employees

These reports are generated using **GROUP BY** together with **COUNT()**, **SUM()**, **AVG()**, and **HAVING**.