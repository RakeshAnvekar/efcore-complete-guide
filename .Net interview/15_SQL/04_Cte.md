# 🚀 SQL Module 4 – CTE (Common Table Expression)

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Mid → Senior .NET Developer
>
> **Key Concept:** A CTE (Common Table Expression) is a temporary named result set that exists only for the duration of a single SQL statement. It makes complex queries easier to read, reuse, and maintain.

---

# 📚 Table of Contents

- What is CTE?
- Why Do We Need CTE?
- Syntax
- Simple CTE
- Multiple CTEs
- CTE with JOIN
- CTE with Window Functions
- Recursive CTE
- CTE vs Subquery
- CTE vs Temp Table
- Real-Time Examples
- Common Interview Questions
- Summary
- Interview One-Liner

---

# 🎯 What is CTE?

CTE stands for

```
Common Table Expression
```

It is a **temporary result set** that exists only during the execution of a single SQL statement.

Think of it like a temporary table.

```
Employee Table

↓

CTE

↓

Final Query
```

---

# Why Do We Need CTE?

Without CTE

```sql
SELECT *
FROM
(
    SELECT *,
           ROW_NUMBER() OVER(ORDER BY Salary DESC) AS RowNo
    FROM Employee
) T
WHERE RowNo <= 3;
```

Hard to read.

With CTE

```sql
WITH EmployeeRank AS
(
    SELECT *,
           ROW_NUMBER() OVER(ORDER BY Salary DESC) AS RowNo
    FROM Employee
)

SELECT *

FROM EmployeeRank

WHERE RowNo <= 3;
```

Much cleaner.

---

# Sample Employee Table

| Id | Name | Department | Salary |
|----|------|------------|--------|
|1|John|HR|50000|
|2|David|IT|70000|
|3|Alex|HR|45000|
|4|Smith|IT|80000|
|5|Bob|Sales|60000|

---

# Syntax

```sql
WITH CTE_Name AS
(
    SELECT ...
)

SELECT *

FROM CTE_Name;
```

---

# Simple CTE

```sql
WITH EmployeeCTE AS
(
    SELECT *

    FROM Employee
)

SELECT *

FROM EmployeeCTE;
```

Execution

```
Employee Table

↓

EmployeeCTE

↓

Final Output
```

---

# CTE with WHERE

```sql
WITH HighSalary AS
(
    SELECT *

    FROM Employee

    WHERE Salary > 50000
)

SELECT *

FROM HighSalary;
```

Output

| Name | Salary |
|------|--------|
|David|70000|
|Smith|80000|
|Bob|60000|

---

# CTE with JOIN

Employee

Department

↓

Join

↓

CTE

↓

Final Result

```sql
WITH EmployeeDepartment AS
(
SELECT e.Name,

d.DepartmentName,

e.Salary

FROM Employee e

INNER JOIN Department d

ON e.DepartmentId=d.DepartmentId
)

SELECT *

FROM EmployeeDepartment;
```

---

# CTE with Window Function ⭐⭐⭐⭐⭐

Most Interviewed Example

```sql
WITH EmployeeRank AS
(
SELECT Name,

Salary,

ROW_NUMBER()

OVER(ORDER BY Salary DESC)

AS RowNo

FROM Employee
)

SELECT *

FROM EmployeeRank

WHERE RowNo<=3;
```

Output

Top 3 Employees.

---

# Find Second Highest Salary ⭐⭐⭐⭐⭐

```sql
WITH SalaryRank AS
(
SELECT Name,

Salary,

DENSE_RANK()

OVER(ORDER BY Salary DESC)

AS RankNo

FROM Employee
)

SELECT *

FROM SalaryRank

WHERE RankNo=2;
```

---

# Multiple CTEs

You can define multiple CTEs.

```sql
WITH HighSalary AS
(
SELECT *

FROM Employee

WHERE Salary>50000
),

ITEmployees AS
(
SELECT *

FROM HighSalary

WHERE Department='IT'
)

SELECT *

FROM ITEmployees;
```

Execution

```
Employee

↓

HighSalary

↓

ITEmployees

↓

Result
```

---

# Recursive CTE ⭐⭐⭐⭐⭐

Used for hierarchical data.

Employee

| Id | Name | ManagerId |
|----|------|-----------|
|1|John|NULL|
|2|David|1|
|3|Alex|2|

Query

```sql
WITH EmployeeHierarchy AS
(
SELECT Id,

Name,

ManagerId

FROM Employee

WHERE ManagerId IS NULL

UNION ALL

SELECT e.Id,

e.Name,

e.ManagerId

FROM Employee e

INNER JOIN EmployeeHierarchy h

ON e.ManagerId=h.Id
)

SELECT *

FROM EmployeeHierarchy;
```

Used for

- Employee Hierarchy
- Folder Structure
- Categories
- Organization Charts

---

# CTE vs Subquery ⭐⭐⭐⭐⭐

| CTE | Subquery |
|------|----------|
| Easy to Read | Hard to Read |
| Reusable | Not Reusable |
| Better for Complex Queries | Good for Small Queries |
| Supports Recursion | No |

---

# CTE vs Temp Table ⭐⭐⭐⭐⭐

| CTE | Temp Table |
|------|------------|
| Temporary Query Result | Temporary Physical Table |
| Exists During One Query | Exists Until Dropped/Session Ends |
| No Index | Can Have Index |
| No Statistics | Has Statistics |
| Faster for Small Queries | Better for Large Data |

---

# Real-Time Example 1

Top 5 Highest Paid Employees

```sql
WITH EmployeeRank AS
(
SELECT *,

ROW_NUMBER()

OVER(ORDER BY Salary DESC)

AS RowNo

FROM Employee
)

SELECT *

FROM EmployeeRank

WHERE RowNo<=5;
```

---

# Real-Time Example 2

Sales Report

```
Orders

↓

Monthly Sales

↓

CTE

↓

Final Dashboard
```

---

# Real-Time Example 3

Employee Hierarchy

```
CEO

↓

Manager

↓

Lead

↓

Developer
```

Recursive CTE is used.

---

# Common Interview Questions

## What is CTE?

A temporary named result set used within a single SQL statement.

---

## Why use CTE?

To improve readability and simplify complex SQL queries.

---

## Can CTE replace a Temp Table?

No.

CTEs are temporary query results.

Temp Tables physically store data temporarily.

---

## Can we update data using CTE?

Yes.

Example

```sql
WITH EmployeeCTE AS
(
SELECT *

FROM Employee

WHERE Department='HR'
)

UPDATE EmployeeCTE

SET Salary=Salary+5000;
```

---

## Can CTE call another CTE?

Yes.

Multiple CTEs can reference previous CTEs.

---

## Can CTE be recursive?

Yes.

Recursive CTEs are commonly used for hierarchical data.

---

# 📌 Summary

| Requirement | Recommended |
|-------------|-------------|
| Improve Readability | CTE |
| Complex SQL | CTE |
| Recursive Queries | Recursive CTE |
| Top N Records | CTE + ROW_NUMBER |
| Second Highest Salary | CTE + DENSE_RANK |
| Small Temporary Result | CTE |
| Large Temporary Data | Temp Table |

---

# 🎯 Interview One-Liner

> **A Common Table Expression (CTE) is a temporary named result set used to simplify complex SQL queries. It improves readability, supports recursion, works well with Window Functions, and is commonly used for Top-N queries, hierarchical data, and reporting.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

## "Where have you used CTE in a real project?"

Answer:

> In reporting modules, I frequently use CTEs with `ROW_NUMBER()` and `DENSE_RANK()` to retrieve the Top N records, generate department-wise rankings, and find the second highest salary. CTEs make complex SQL much more readable and easier to maintain than deeply nested subqueries.