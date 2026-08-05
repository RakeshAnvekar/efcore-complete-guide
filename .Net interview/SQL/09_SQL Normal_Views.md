# SQL Server Views – Complete Interview Guide (README 1)

## Module 13.1 – SQL Views Fundamentals

> **Interview Frequency:** ⭐⭐⭐⭐⭐

Views are one of the most commonly used database objects in enterprise applications. They simplify complex queries, improve security, and provide a stable interface between applications and the underlying database.

---

# Table of Contents

1. What is a View?
2. Why Do We Need Views?
3. How Views Work Internally
4. View Architecture
5. Creating Your First View
6. Querying a View
7. Why a View is Called a Virtual Table
8. Real-World Examples
9. Advantages of Views
10. Disadvantages of Views
11. Common Use Cases
12. Interview Questions
13. Summary

---

# 1. What is a View?

A **View** is a **virtual table** whose contents are defined by a SQL query.

Unlike a normal table:

* A View **does not store data** (except Indexed Views).
* It stores only the **SELECT statement**.
* Whenever a View is queried, SQL Server executes the stored query.

Think of a View as a **saved SELECT statement**.

---

# 2. Why Do We Need Views?

Suppose every screen in your application needs employee information with department names.

Without a View, every developer writes:

```sql
SELECT
    e.EmployeeId,
    e.Name,
    d.DepartmentName
FROM Employee e
INNER JOIN Department d
    ON e.DepartmentId = d.DepartmentId;
```

If 50 reports need the same query:

* The SQL is duplicated.
* Maintenance becomes difficult.
* Bugs increase.

Instead:

```sql
CREATE VIEW vwEmployeeDetails
AS
SELECT
    e.EmployeeId,
    e.Name,
    d.DepartmentName
FROM Employee e
INNER JOIN Department d
    ON e.DepartmentId = d.DepartmentId;
```

Now everyone simply writes:

```sql
SELECT *
FROM vwEmployeeDetails;
```

---

# 3. How Views Work Internally

Suppose the following View exists:

```sql
CREATE VIEW vwEmployee
AS
SELECT *
FROM Employee;
```

When you execute:

```sql
SELECT *
FROM vwEmployee;
```

SQL Server does **NOT** read data from the View.

Instead it expands the View into its underlying query.

Execution Flow:

```text
Application

      │

SELECT * FROM vwEmployee

      │

      ▼

Read View Definition

      │

      ▼

SELECT * FROM Employee

      │

      ▼

Optimizer Creates Execution Plan

      │

      ▼

Read Employee Table

      │

      ▼

Return Result
```

The View itself stores **no employee rows**.

---

# 4. View Architecture

```text
                Application

                     │

                     ▼

             SELECT * FROM View

                     │

                     ▼

              SQL Server Engine

                     │

          Reads View Definition

                     │

                     ▼

         Underlying SQL Statement

                     │

                     ▼

        Employee      Department

             │            │

             └──────JOIN──┘

                     │

                     ▼

               Execution Plan

                     │

                     ▼

               Return Result
```

---

# 5. Creating Your First View

Employee Table

```text
EmployeeId    Name     DepartmentId

1             John     10

2             David    20
```

Department Table

```text
DepartmentId    DepartmentName

10              HR

20              IT
```

Create View

```sql
CREATE VIEW vwEmployeeDetails
AS
SELECT
    e.EmployeeId,
    e.Name,
    d.DepartmentName
FROM Employee e
INNER JOIN Department d
ON e.DepartmentId=d.DepartmentId;
```

Query

```sql
SELECT *
FROM vwEmployeeDetails;
```

Output

```text
EmployeeId    Name     DepartmentName

1             John     HR

2             David    IT
```

---

# 6. Why is a View Called a Virtual Table?

A normal table physically stores rows.

Example:

```text
Employee Table

John

David

Alex
```

The View stores:

```text
SELECT Statement Only
```

Whenever queried:

```text
View

↓

Execute SELECT Statement

↓

Read Base Tables

↓

Return Rows
```

Nothing is permanently stored inside the View.

---

# 7. Real-World Example

## Amazon Order Dashboard

Tables:

* Orders
* Customers
* Payments
* Products
* Shipping

Without a View

Every report contains:

```text
Orders

JOIN Customers

JOIN Products

JOIN Payments

JOIN Shipping
```

Instead create:

```text
vw_OrderSummary
```

Now developers simply execute:

```sql
SELECT *
FROM vw_OrderSummary;
```

This centralizes the business logic.

---

# 8. Does a View Store Data?

This is one of the most common interview questions.

## Normal View

```text
Stores Query Definition

NO Data
```

## Indexed View

```text
Stores Query Definition

+

Physically Stores Data
```

Normal Views never copy table data.

---

# 9. What Happens When Base Table Changes?

Employee Table

```text
John
```

Execute

```sql
UPDATE Employee
SET Name='Johnny'
WHERE EmployeeId=1;
```

Now execute

```sql
SELECT *
FROM vwEmployeeDetails;
```

Result

```text
Johnny
```

Why?

Because SQL Server executes the View query every time.

---

# 10. Advantages of Views

### Simplifies Queries

Instead of writing complex joins repeatedly, developers query the View.

---

### Security

Employee Table

```text
EmployeeId

Name

Salary

PAN

BankAccount
```

View

```text
EmployeeId

Name
```

Applications only see safe columns.

---

### Reusability

Business logic exists in one place.

---

### Easier Maintenance

If the underlying query changes, only the View is updated.

Applications continue to use the same View.

---

### Reporting

Large reports become easier to build using Views.

---

### Backward Compatibility

Suppose the table name changes.

Old Table

```text
Employee
```

New Table

```text
EmployeeMaster
```

Create

```sql
CREATE VIEW Employee
AS
SELECT *
FROM EmployeeMaster;
```

Old applications continue to work.

---

# 11. Disadvantages of Views

* Normal Views do not improve performance.
* Complex Views may generate expensive execution plans.
* Too many nested Views become difficult to maintain.
* Not every View is updatable.
* Debugging deeply nested Views can be challenging.

---

# 12. Common Enterprise Use Cases

### Banking

Customer Summary View

```text
Customer

Accounts

Loans

Cards

Transactions
```

---

### Healthcare

Patient Summary View

```text
Patient

Doctor

Appointments

Medicines
```

---

### E-commerce

Order Summary View

```text
Orders

Products

Customers

Payments

Shipping
```

---

### HR

Employee Information View

```text
Employee

Department

Manager

Location
```

---

# 13. Interview Questions

### What is a View?

A View is a virtual table based on the result of a SQL query.

---

### Does a View store data?

A normal View does **not** store data.

Only Indexed Views physically store data.

---

### Why use Views?

* Simplify SQL
* Reuse business logic
* Improve security
* Hide complexity
* Support reporting
* Provide backward compatibility

---

### Is a View faster than a Table?

No.

A normal View is expanded into its underlying query during execution.

Performance depends on the underlying query and indexes.

---

### Why is a View called a Virtual Table?

Because it behaves like a table but stores only the query definition, not the rows.

---

### Can multiple tables be used inside a View?

Yes.

A View can join multiple tables.

---

### What happens when the underlying table changes?

The View immediately reflects the latest data because it executes the underlying query every time.

---

# Key Takeaways

* A View is a **virtual table**.
* A normal View stores **only the SQL query**.
* SQL Server executes the View's query every time it is accessed.
* Views simplify complex SQL and centralize business logic.
* Views improve security by exposing only required columns.
* Normal Views do **not** improve performance.
* Indexed Views are the exception because they physically store data.
* Views are widely used in enterprise applications for reporting, security, and abstraction.
