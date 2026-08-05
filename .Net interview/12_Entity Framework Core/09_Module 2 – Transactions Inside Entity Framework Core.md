# Entity Framework Core – Transactions
# Module 2 – Transactions Inside Entity Framework Core

> **Course:** Entity Framework Core Complete Interview Master Roadmap

**Module:** 2 – Transactions Inside Entity Framework Core

**Difficulty:** ⭐⭐⭐⭐☆

**Interview Importance:** ⭐⭐⭐⭐⭐

---

# Table of Contents

1. What is an Implicit Transaction?
2. Does SaveChanges() Create a Transaction?
3. Internal SaveChanges() Pipeline
4. SQL Generated
5. Single SaveChanges()
6. Multiple Operations in SaveChanges()
7. Multiple SaveChanges()
8. Rollback Behavior
9. Internal Working
10. Best Practices
11. Interview Questions
12. Summary
13. Key Takeaways

---

# What is an Implicit Transaction?

An **Implicit Transaction** is a transaction automatically created by Entity Framework Core.

You don't write

```csharp
BeginTransaction();
```

or

```csharp
Commit();
```

Entity Framework does it for you.

Example

```csharp
context.SaveChanges();
```

Internally

```
Begin Transaction

↓

Execute SQL

↓

Commit

OR

Rollback
```

Everything happens automatically.

---

# Does SaveChanges() Create a Transaction?

Yes.

Whenever SaveChanges() needs to execute multiple SQL statements,

Entity Framework automatically wraps them inside a transaction.

Example

```csharp
context.Orders.Add(order);

context.Payments.Add(payment);

context.SaveChanges();
```

Internally

```
Begin Transaction

↓

INSERT Order

↓

INSERT Payment

↓

Commit
```

You never see the transaction,

but it exists.

---

# SaveChanges() Internal Pipeline

Suppose

```csharp
context.Orders.Add(order);

context.Payments.Add(payment);

context.SaveChanges();
```

Internally

```
SaveChanges()

↓

DetectChanges()

↓

Find Added Entities

↓

Generate SQL

↓

Open Database Connection

↓

Begin Transaction

↓

Execute SQL

↓

Commit

↓

AcceptAllChanges()

↓

Return Rows Affected
```

---

# Internal Flow Diagram

```
Application

↓

DbContext

↓

SaveChanges()

↓

DetectChanges()

↓

Generate SQL

↓

Open Connection

↓

Begin Transaction

↓

Execute SQL

↓

Commit

↓

AcceptAllChanges()

↓

Dispose Transaction
```

---

# Example 1 – Single Insert

```csharp
var employee = new Employee
{
    Name = "Rakesh"
};

context.Employees.Add(employee);

context.SaveChanges();
```

Generated SQL

```sql
BEGIN TRANSACTION;

INSERT INTO Employees(Name)
VALUES('Rakesh');

COMMIT;
```

If INSERT fails

```sql
ROLLBACK;
```

---

# Example 2 – Multiple Inserts

```csharp
context.Employees.Add(employee1);

context.Employees.Add(employee2);

context.Employees.Add(employee3);

context.SaveChanges();
```

Generated SQL

```sql
BEGIN TRANSACTION;

INSERT Employee1;

INSERT Employee2;

INSERT Employee3;

COMMIT;
```

If Employee2 fails

```sql
ROLLBACK;
```

Database

```
Employee1 ❌

Employee2 ❌

Employee3 ❌
```

Nothing is saved.

---

# Example 3 – Insert + Update + Delete

```csharp
context.Orders.Add(order);

context.Products.Update(product);

context.Customers.Remove(customer);

context.SaveChanges();
```

Generated SQL

```sql
BEGIN TRANSACTION;

INSERT INTO Orders...

UPDATE Products...

DELETE Customers...

COMMIT;
```

If DELETE fails

```
ROLLBACK

↓

INSERT Reverted

UPDATE Reverted
```

Everything returns to its previous state.

---

# What Happens if SaveChanges() Fails?

Suppose

```
INSERT Order

↓

UPDATE Inventory

↓

SQL Exception
```

Entity Framework automatically performs

```
Rollback
```

Final Database

```
Order

Not Saved

Inventory

Not Updated
```

Everything remains consistent.

---

# Multiple SaveChanges()

Suppose

```csharp
context.Orders.Add(order);

context.SaveChanges();

context.Payments.Add(payment);

context.SaveChanges();
```

Question

How many transactions?

Answer

```
2 Transactions
```

Flow

```
SaveChanges()

↓

Transaction 1

↓

Commit

------------------

SaveChanges()

↓

Transaction 2

↓

Commit
```

Each SaveChanges() creates its own transaction.

---

# Real-Time Banking Example

Suppose

```csharp
DebitAccount();

context.SaveChanges();

CreditAccount();

context.SaveChanges();
```

Transaction Flow

```
Transaction 1

↓

Debit

↓

Commit

-------------------

Transaction 2

↓

Credit

↓

Commit
```

Problem

If Transaction 2 fails

```
Money Debited

Money Not Credited
```

Database becomes inconsistent.

This is why multiple SaveChanges() can be dangerous.

---

# Better Approach

```csharp
DebitAccount();

CreditAccount();

context.SaveChanges();
```

Only one transaction

```
Begin Transaction

↓

Debit

↓

Credit

↓

Commit
```

If Credit fails

```
Rollback

↓

Debit Undone
```

Everything remains consistent.

---

# Internal Working

```
DbContext

↓

Track Entities

↓

SaveChanges()

↓

DetectChanges()

↓

Generate SQL

↓

Begin Transaction

↓

Execute SQL Statements

↓

Success?

↓

YES

↓

Commit

↓

AcceptAllChanges()

----------------------

NO

↓

Rollback

↓

Throw Exception
```

---

# SaveChangesAsync()

```csharp
await context.SaveChangesAsync();
```

Pipeline

```
DetectChanges()

↓

Generate SQL

↓

Begin Transaction

↓

Execute SQL Asynchronously

↓

Commit

↓

AcceptAllChanges()
```

Everything is the same,

only SQL execution is asynchronous.

---

# Real-Time E-Commerce Example

Customer Places Order

Operations

```
Insert Order

↓

Insert Payment

↓

Reduce Inventory

↓

SaveChanges()
```

One transaction.

If Inventory fails

```
Rollback

↓

Order Removed

↓

Payment Removed
```

No partial data.

---

# Best Practices

✔ Call SaveChanges() once whenever possible.

✔ Group related database operations together.

✔ Avoid multiple SaveChanges() for one business operation.

✔ Use SaveChangesAsync() in ASP.NET Core.

✔ Let EF manage transactions unless you need explicit control.

---

# Interview Questions

## Does SaveChanges() automatically create a transaction?

Yes.

Entity Framework Core automatically creates an implicit transaction when SaveChanges() executes multiple database operations.

---

## What is an Implicit Transaction?

A transaction automatically created and managed by Entity Framework Core.

---

## What happens if one SQL statement fails?

Entity Framework rolls back the entire transaction.

---

## Does every SaveChanges() create a new transaction?

Yes.

Each SaveChanges() call creates its own transaction.

---

## Is multiple SaveChanges() a good practice?

Generally, no.

If all operations belong to the same business process, use one SaveChanges() so everything is committed or rolled back together.

---

# Summary

Entity Framework Core automatically manages transactions through **Implicit Transactions**.

Whenever SaveChanges() executes database operations, EF Core begins a transaction, executes all SQL statements, commits if everything succeeds, or rolls back if any statement fails.

Calling SaveChanges() multiple times creates multiple independent transactions, which may lead to inconsistent data if one succeeds and another fails.

---

# Key Takeaways

✔ SaveChanges() automatically creates an implicit transaction.

✔ You don't need BeginTransaction() for simple CRUD.

✔ One SaveChanges() = One Transaction.

✔ Multiple SaveChanges() = Multiple Transactions.

✔ Failure automatically triggers Rollback.

✔ Success automatically triggers Commit.

✔ SaveChangesAsync() follows the same transaction pipeline asynchronously.

---

# Next Module

## Module 3 – Explicit Transactions

Topics Covered

- BeginTransaction()
- Commit()
- Rollback()
- Multiple SaveChanges() inside one transaction
- Multiple DbContexts
- Real Banking Example
- Manual Transaction Control
- Interview Questions