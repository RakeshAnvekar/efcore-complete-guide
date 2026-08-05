# 🚀 SQL Module 3 – Window Functions

> **Difficulty:** ⭐⭐⭐⭐⭐
>
> **Interview Level:** Mid → Senior .NET Developer
>
> **Key Concept:** Window Functions perform calculations across a set of rows without collapsing them into a single row like GROUP BY.

---

# 📚 Table of Contents

- What are Window Functions?
- Why Do We Need Window Functions?
- OVER()
- ROW_NUMBER()
- RANK()
- DENSE_RANK()
- PARTITION BY
- ROW_NUMBER vs RANK vs DENSE_RANK
- Real-Time Examples
- Common Interview Questions
- Summary

---

# Sample Employee Table

| Id | Name | Department | Salary |
|----|------|------------|--------|
|1|John|HR|50000|
|2|David|IT|70000|
|3|Alex|HR|50000|
|4|Smith|IT|70000|
|5|Bob|HR|45000|
|6|Kevin|IT|60000|

---

# 🎯 What are Window Functions?

Window Functions perform calculations over a group of rows while keeping every row in the result.

Unlike GROUP BY,

GROUP BY

```
6 Rows

↓

3 Rows
```

Window Function

```
6 Rows

↓

Still 6 Rows
```

---

# OVER()

Every window function uses

```sql
OVER()
```

Syntax

```sql
Function()

OVER(...)
```

---

# ROW_NUMBER() ⭐⭐⭐⭐⭐

Assigns a unique sequential number.

```sql
SELECT Name,
       Salary,
       ROW_NUMBER()
       OVER(ORDER BY Salary DESC) AS RowNo
FROM Employee;
```

Output

| Name | Salary | RowNo |
|------|--------|-------|
|David|70000|1|
|Smith|70000|2|
|Kevin|60000|3|
|John|50000|4|
|Alex|50000|5|
|Bob|45000|6|

Notice

Even equal salaries get different row numbers.

---

# RANK() ⭐⭐⭐⭐⭐

Same salaries receive the same rank.

```sql
SELECT Name,
       Salary,
       RANK()
       OVER(ORDER BY Salary DESC) AS RankNo
FROM Employee;
```

Output

| Name | Salary | Rank |
|------|--------|------|
|David|70000|1|
|Smith|70000|1|
|Kevin|60000|3|
|John|50000|4|
|Alex|50000|4|
|Bob|45000|6|

Notice

Rank **2** and **5** are skipped.

---

# DENSE_RANK() ⭐⭐⭐⭐⭐

Same salaries receive the same rank.

No gaps.

```sql
SELECT Name,
       Salary,
       DENSE_RANK()
       OVER(ORDER BY Salary DESC) AS RankNo
FROM Employee;
```

Output

| Name | Salary | DenseRank |
|------|--------|-----------|
|David|70000|1|
|Smith|70000|1|
|Kevin|60000|2|
|John|50000|3|
|Alex|50000|3|
|Bob|45000|4|

No missing numbers.

---

# PARTITION BY ⭐⭐⭐⭐⭐

Partition means

Create separate groups.

Example

Rank employees department-wise.

```sql
SELECT Name,
       Department,
       Salary,
       ROW_NUMBER()
       OVER
       (
           PARTITION BY Department
           ORDER BY Salary DESC
       ) AS RowNo
FROM Employee;
```

Output

HR

| Name | Salary | RowNo |
|------|--------|-------|
|John|50000|1|
|Alex|50000|2|
|Bob|45000|3|

IT

| Name | Salary | RowNo |
|------|--------|-------|
|David|70000|1|
|Smith|70000|2|
|Kevin|60000|3|

Execution

```
Employee

↓

Partition By Department

↓

HR Group

↓

Ranking

----------------

IT Group

↓

Ranking
```

---

# Find 2nd Highest Salary ⭐⭐⭐⭐⭐

Using DENSE_RANK

```sql
WITH SalaryRank AS
(
SELECT Name,
Salary,
DENSE_RANK()
OVER(ORDER BY Salary DESC) AS RankNo
FROM Employee
)

SELECT *

FROM SalaryRank

WHERE RankNo = 2;
```

---

# Top 3 Salaries

```sql
WITH SalaryRank AS
(
SELECT *,
ROW_NUMBER()
OVER(ORDER BY Salary DESC) AS RowNo
FROM Employee
)

SELECT *

FROM SalaryRank

WHERE RowNo <= 3;
```

---

# Top Salary Per Department

```sql
WITH SalaryRank AS
(
SELECT *,
ROW_NUMBER()
OVER
(
PARTITION BY Department
ORDER BY Salary DESC
) AS RowNo
FROM Employee
)

SELECT *

FROM SalaryRank

WHERE RowNo = 1;
```

---

# ROW_NUMBER vs RANK vs DENSE_RANK

| Feature | ROW_NUMBER | RANK | DENSE_RANK |
|----------|------------|------|------------|
| Duplicate Salary | Different Numbers | Same Rank | Same Rank |
| Gap in Ranking | No | Yes | No |
| Most Used | Yes | Yes | Yes |

Example

Salary

```
70000

70000

60000

50000
```

ROW_NUMBER

```
1

2

3

4
```

RANK

```
1

1

3

4
```

DENSE_RANK

```
1

1

2

3
```

---

# Real-Time Examples

Employee Ranking

```sql
ROW_NUMBER()
```

---

Top Salary Per Department

```sql
PARTITION BY
```

---

Leaderboard

```sql
RANK()
```

---

Competition Ranking

```sql
DENSE_RANK()
```

---

# Common Interview Questions

## What is a Window Function?

Performs calculations across rows without reducing the number of rows.

---

## Difference between ROW_NUMBER and RANK?

ROW_NUMBER gives every row a unique number.

RANK gives equal values the same rank.

---

## Difference between RANK and DENSE_RANK?

RANK skips numbers.

DENSE_RANK does not.

---

## What is PARTITION BY?

Creates separate groups before applying a window function.

---

## How do you find the 2nd highest salary?

Using DENSE_RANK() or ROW_NUMBER() with a CTE.

---

# 📌 Summary

| Requirement | Function |
|-------------|----------|
| Unique Number | ROW_NUMBER |
| Competition Ranking | RANK |
| Continuous Ranking | DENSE_RANK |
| Department-wise Ranking | PARTITION BY |
| Second Highest Salary | DENSE_RANK |
| Top N Records | ROW_NUMBER |

---

# 🎯 Interview One-Liner

> **Window Functions perform calculations across rows without collapsing them into a single row. `ROW_NUMBER()` assigns unique numbers, `RANK()` allows ties with gaps, `DENSE_RANK()` allows ties without gaps, and `PARTITION BY` creates separate groups before applying the window function.**

---

# ⭐ Senior Interview Tip

If the interviewer asks:

**"Which window function do you use most?"**

Answer:

- **ROW_NUMBER()** → Pagination, Top N records
- **RANK()** → Competition ranking
- **DENSE_RANK()** → Second Highest Salary, Salary ranking
- **PARTITION BY** → Department-wise reports, Top salary per department